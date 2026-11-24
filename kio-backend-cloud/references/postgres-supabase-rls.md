# PostgreSQL & Supabase RLS Deep Dive

## 1. Security Definer vs Security Invoker Functions
- **Security Invoker (Default)**: Function executes with the permissions of the calling user. Respects RLS.
- **Security Definer**: Function executes with the privileges of the creator (postgres/superuser). **Bypasses RLS**.
  - Always specify `SET search_path = public, pg_temp;` inside `SECURITY DEFINER` functions to prevent search path injection attacks.

```sql
CREATE OR REPLACE FUNCTION public.handle_new_user()
RETURNS TRIGGER
LANGUAGE plpgsql
SECURITY DEFINER
SET search_path = public, pg_temp
AS $$
BEGIN
  INSERT INTO public.profiles (id, email, full_name)
  VALUES (new.id, new.email, new.raw_user_meta_data->>'full_name');
  RETURN NEW;
END;
$$;
```

## 2. Multi-Role RBAC Policy Template
```sql
-- Read: Anyone can read published items
CREATE POLICY "Public read published" ON articles
  FOR SELECT USING (status = 'published');

-- Write: Only authors can update their own drafts
CREATE POLICY "Authors update drafts" ON articles
  FOR UPDATE USING (
    author_id = (SELECT auth.uid())
    AND status = 'draft'
  )
  WITH CHECK (                      -- without this, an author could reassign author_id or publish
    author_id = (SELECT auth.uid())
    AND status = 'draft'
  );

-- Admin: Superusers/Admins can do everything
CREATE POLICY "Admins full access" ON articles
  FOR ALL USING (
    EXISTS (
      SELECT 1 FROM user_roles
      WHERE user_id = (SELECT auth.uid())
      AND role = 'admin'
    )
  );
```

## 3. Baseline every table
```sql
ALTER TABLE public.articles ENABLE ROW LEVEL SECURITY;   -- no policy = no access (except service_role)
-- Optional: also apply RLS to the table owner
ALTER TABLE public.articles FORCE ROW LEVEL SECURITY;

-- Policies target roles explicitly
CREATE POLICY "Own rows read" ON public.notes
  FOR SELECT TO authenticated
  USING (user_id = (SELECT auth.uid()));

CREATE POLICY "Own rows insert" ON public.notes
  FOR INSERT TO authenticated
  WITH CHECK (user_id = (SELECT auth.uid()));
```
- `USING` filters rows you can see/target; `WITH CHECK` validates the row **after** insert/update. `INSERT` needs `WITH CHECK`, `UPDATE` needs both.
- Wrap `auth.uid()` / `auth.jwt()` in `(SELECT ...)` so Postgres evaluates it once per query, not once per row.
- Index every column used in policies (`user_id`, `org_id`) — RLS predicates are just `WHERE` clauses.

## 4. Multi-tenant (organisation) pattern
```sql
CREATE TABLE public.memberships (
  org_id  uuid NOT NULL REFERENCES orgs(id),
  user_id uuid NOT NULL REFERENCES auth.users(id),
  role    text NOT NULL CHECK (role IN ('owner','admin','member')),
  PRIMARY KEY (org_id, user_id)
);
CREATE INDEX ON public.memberships (user_id);

-- Helper avoids recursive RLS on memberships and keeps policies short
CREATE OR REPLACE FUNCTION public.user_org_ids()
RETURNS SETOF uuid
LANGUAGE sql STABLE SECURITY DEFINER
SET search_path = public, pg_temp
AS $$ SELECT org_id FROM public.memberships WHERE user_id = (SELECT auth.uid()) $$;
REVOKE ALL ON FUNCTION public.user_org_ids() FROM public;
GRANT EXECUTE ON FUNCTION public.user_org_ids() TO authenticated;

CREATE POLICY "Org members read projects" ON public.projects
  FOR SELECT TO authenticated
  USING (org_id IN (SELECT public.user_org_ids()));
```

## 5. Roles, JWT claims and storage
- Do **not** authorise from `raw_user_meta_data` / `user_metadata`: users can edit it. Use a table you control or `app_metadata` (set server-side), read via `auth.jwt() -> 'app_metadata'`.
- The `service_role` key bypasses RLS: server-only, never in the browser or mobile bundle. The `anon` key is public by design — RLS is the only thing protecting data.
- Storage buckets use policies on `storage.objects`, e.g. `bucket_id = 'avatars' AND (storage.foldername(name))[1] = (SELECT auth.uid())::text`.
- Views run with the owner's rights unless created `WITH (security_invoker = true)` (PG 15+) — otherwise a view can leak rows past RLS.

## 6. Test policies like code
```sql
BEGIN;
SET LOCAL role authenticated;
SELECT set_config('request.jwt.claims', '{"sub":"11111111-1111-1111-1111-111111111111","role":"authenticated"}', true);
SELECT count(*) FROM public.notes;               -- only that user's rows
INSERT INTO public.notes(user_id, body)
VALUES ('22222222-2222-2222-2222-222222222222', 'x'); -- must fail WITH CHECK
ROLLBACK;
```
Automate with pgTAP (`supabase test db`). Run Supabase's security advisor (`supabase db lint`) to catch tables with RLS disabled.
