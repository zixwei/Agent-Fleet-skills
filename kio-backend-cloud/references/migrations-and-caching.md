# Zero-Downtime Migrations & Redis Patterns

## 1. Safe Schema Migration Playbook
When renaming or dropping columns in high-traffic applications:
1. **Phase 1 (Add)**: Add the new column as nullable.
2. **Phase 2 (Dual Write)**: Update app code to write to both old and new columns, reading from old.
3. **Phase 3 (Backfill)**: Run asynchronous background script in small batches (e.g. 1,000 rows/batch) to copy old data to new.
4. **Phase 4 (Read New)**: Switch app reads to the new column.
5. **Phase 5 (Deprecate Old)**: Stop writing to old column, then safely DROP COLUMN.

## 2. Redis Distributed Locking (Redlock)
```typescript
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL);

async function acquireLock(lockKey: string, ttlMs: number): Promise<string | null> {
  const token = crypto.randomUUID();   // unguessable owner token (Node 19+ global crypto)
  const result = await redis.set(lockKey, token, 'PX', ttlMs, 'NX');
  return result === 'OK' ? token : null;
}

async function releaseLock(lockKey: string, token: string): Promise<boolean> {
  const luaScript = `
    if redis.call("get", KEYS[1]) == ARGV[1] then
      return redis.call("del", KEYS[1])
    else
      return 0
    end
  `;
  const result = await redis.eval(luaScript, 1, lockKey, token);
  return result === 1;
}
```

> A single-node `SET NX PX` lock is fine for "do not run this cron twice". It is **not** a correctness guarantee across Redis failover; for money or inventory, rely on database constraints/transactions (`SELECT ... FOR UPDATE`, unique keys) and treat the Redis lock as an optimisation.

## 3. Lock-safe DDL in PostgreSQL
| Change | Safe way |
|---|---|
| Add column | `ADD COLUMN ... NULL` or with a constant `DEFAULT` (instant on PG 11+). No volatile defaults on big tables. |
| Add `NOT NULL` | `ADD CONSTRAINT c CHECK (col IS NOT NULL) NOT VALID;` → `VALIDATE CONSTRAINT c;` → `ALTER COLUMN col SET NOT NULL;` (PG 12+ uses the validated check, no full scan) |
| Add foreign key | `ADD CONSTRAINT fk ... NOT VALID;` then `VALIDATE CONSTRAINT fk;` |
| Add index | `CREATE INDEX CONCURRENTLY` (non-transactional migration) |
| Change column type | New column + backfill + swap (the 5-phase playbook above) |
| Rename column/table | Expand/contract: add new, dual-write, switch reads, drop old. Never rename in one deploy. |

Every migration session starts with:
```sql
SET lock_timeout = '5s';
SET statement_timeout = '15min';
```

Backfill in batches with a pause, by primary key range:
```sql
UPDATE users SET email_normalized = lower(email)
WHERE id > $1 AND id <= $1 + 1000 AND email_normalized IS NULL;
```

## 4. Caching patterns
- **Cache-aside (default):** read cache → miss → read DB → `SET key value EX ttl`. On write: update DB, then `DEL` the key (do not write the cache from the write path).
- **TTL + jitter:** `ttl = base + random(0, base*0.1)` so keys do not expire together.
- **Stampede protection:** on miss, take a short lock (`SET lock:key token NX PX 5000`); other callers serve stale data or wait briefly.
- **Key design:** `app:v1:user:{id}:profile` — version prefix lets you invalidate a whole shape on deploy.
- **Negative caching:** cache "not found" for a short TTL to stop hammering the DB with missing IDs.
- Never cache per-user authorised data under a key without the user/tenant ID in it.

```ts
async function getProfile(id: string) {
  const key = `app:v1:user:${id}:profile`;
  const hit = await redis.get(key);
  if (hit) return JSON.parse(hit);
  const row = await db.user.findUnique({ where: { id } });
  await redis.set(key, JSON.stringify(row ?? null), 'EX', row ? 300 + Math.floor(Math.random() * 30) : 30);
  return row;
}
```

## 5. Rate limiting (fixed window, atomic)
```ts
const key = `rl:${userId}:${Math.floor(Date.now() / 60000)}`;
const [[, count]] = await redis.multi().incr(key).expire(key, 60).exec();
if ((count as number) > 100) return res.status(429).set('Retry-After', '60').end();
```
