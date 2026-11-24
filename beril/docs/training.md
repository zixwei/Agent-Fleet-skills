# Training with Beril

Beril has a 25-week curriculum and seven local labs. It is a practice system, not a reading list.

## Start

```bash
./beril train --status
./beril train --week 1
./beril labs --run
```

Progress lives in `training/progress.json` and is gitignored. Notes live in `training/notes/`.

## Weekly loop

Monday read the source. Tuesday drills. Wednesday and Thursday lab. Friday report and checkpoint. Weekend redo one old lab.

Mark tasks:

```bash
./beril train --done w1d1
./beril train --log "Bandit 6 done, learned about SUID"
```

## Labs

| Lab | Topic |
|---|---|
| `web` | IDOR, XSS, SQLi, traversal, SSRF |
| `binary` | stack, format string, UAF, integer |
| `fuzz` | libFuzzer and crash triage |
| `re` | stripped binary reconstruction |
| `mobile` | exported components and deep links |
| `firmware` | extraction and CGI injection |
| `cloud` | Terraform, pod, Dockerfile |
| `ad` | ACL path and ADCS |
| `llm` | prompt injection and RAG |
| `detection` | Sigma and log triage |

Each lab has `README.md`, `tests.sh`, and `ANSWERS.md`. Attempt first, then read the answers.

## Evidence

Every task ends with a command, a result, and a file path. A task without evidence is a read, not practice.

## Grading

Score reproduction, evidence, root cause, and communication from 0 to 3. Twelve is ready to move on. Eight to eleven means repeat with a new input. Below eight means repeat the week.

## Rules

- Local only unless a task says otherwise.
- Snapshot before a lab that can crash the host.
- 90 minutes maximum per session.
- One writeup a month.
- Stop at the agreed proof.

## Learn from each lab

```bash
./beril reflect --lab web --finding "IDOR on profile" \
  --root-cause "the query id is used instead of the session id" \
  --evidence labs/web/evidence/idor.txt --fix "scope the query by session uid"
./beril reflect --review
```

See `references/self-training.md`.

## Long game

Read `mastery.md` for the track map and `references/books.md` for the canon.

## Full plan

`references/curriculum.md` has the phases and capstone. `references/exercises.md` has 74 short drills by domain.
