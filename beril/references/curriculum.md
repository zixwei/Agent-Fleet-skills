# Training curriculum

The curriculum turns Beril from a lookup tool into a practice system. It runs 25 weeks, about 10 hours a week, from the shell to exploit development and firmware. Every week ends with evidence, not a feeling of understanding.

The machine-readable plan lives in `data/curriculum.json` and is driven by `./beril train`.

## Rules

1. Two hours a day, five days a week. Miss a day, not a week.
2. Every session ends with a note: command, result, next question.
3. No tool without a hypothesis. No finding without a reproduction.
4. Read the primary source before the video summary.
5. Redo one old lab each week from memory.
6. Publish one writeup a month. Feedback closes the loop.
7. Keep a lab log. The log is the evidence.

## Setup

Use a snapshot-capable VM or container host. Keep the host clean.

```bash
git clone https://github.com/sheduxdev/beril.git
cd beril
./install.sh --tools --re
mkdir -p training/notes
```

Create a snapshot before each lab. Name it after the lab, not the date.

Write a one-page rules-of-engagement template with: target, window, allowed actions, forbidden actions, contact, and stop condition.

## The weekly loop

| Day | Work |
|---|---|
| Monday | Read the week's source, write five questions |
| Tuesday | Drill 1 and drill 2 |
| Wednesday | Lab, first pass |
| Thursday | Lab, second pass, debug the failures |
| Friday | Deliverable, checkpoint, one-paragraph retrospective |
| Weekend | Redo one old lab, read one writeup, rest |

Each session:

```text
Goal:
Hypothesis:
Command:
Result:
Evidence path:
Next question:
```

## Phases

| Phase | Weeks | Outcome |
|---|---|---|
| Setup | 0 | A lab, a log, and rules |
| Foundations | 1 to 4 | Linux, network, HTTP, scripting |
| Recon and web | 5 to 9 | Asset map, web and API findings |
| Code and binary | 10 to 14 | Source audit, RE, fuzzing |
| Exploit and infrastructure | 15 to 19 | Primitive, chain, AD, cloud |
| Specialist | 20 to 23 | Mobile, firmware, detection, AI |
| Mastery | 24 | Report, review, capstone |

## Phase detail

### Setup, week 0

Build the host, install Beril, write the rules. Snapshot. Learn to restore it. A lab you cannot restore is a liability.

### Foundations, weeks 1 to 4

Bandit and the binary lab teach the shell. Wireshark and DNS teach the network. DevTools and CORS teach the browser boundary. Python and Bash teach automation.

Checkpoint at week 4: you can build a small tool, explain a TLS handshake, and read a request without a proxy.

### Recon and web, weeks 5 to 9

Asset mapping, certificate and ASN pivots, content discovery, authentication, injection, logic, IDOR, and APIs. The local web lab covers IDOR, XSS, SQLi, traversal, SSRF, and weak authorization.

Checkpoint at week 9: you can take one request from discovery to a reproduced impact and write the chain without a template.

### Code and binary, weeks 10 to 14

Source audit with semgrep and manual tracing. C memory and mitigations. Ghidra static work. GDB and Frida dynamic work. A real libFuzzer campaign on the local parser.

Checkpoint at week 14: you can recover a hidden check from assembly, confirm it in a debugger, and fuzz a parser to a root cause.

### Exploit and infrastructure, weeks 15 to 19

Crash triage, ROP, shellcode, heap and UAF, Active Directory, cloud, and Kubernetes. The binary lab has a UAF and an integer bug. The cloud lab has public storage and an open security group.

Checkpoint at week 19: a working PoC with reliability numbers, a BloodHound path, and one cloud finding with a read-only proof.

### Specialist, weeks 20 to 23

Mobile components and hooks, firmware extraction and emulation, detection engineering, and LLM application testing.

Checkpoint at week 23: one mobile component finding, one firmware root shell, one Sigma rule, one prompt injection with impact.

### Mastery, week 24

A full report, a peer review, and a five-minute presentation. The capstone is a fresh target from the list below.

## Assessment

Score each week 0 to 3 on four axes:

| Axis | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| Reproduction | Did not run | Ran with help | Ran alone | Ran alone and explained every step |
| Evidence | None | Screenshot | Request and response | Minimal input plus logs |
| Root cause | Guess | Symptom | Mechanism | Mechanism and missing check |
| Communication | None | Bullet notes | Short report | Report a triager can run |

Twelve or more out of twelve is ready to move on. Eight to eleven means repeat the lab with a new input. Below eight means repeat the week.

## Capstone list

Pick one, or ask for a target in scope:

- A local vulnerable web app with three chained findings.
- A CTF binary with NX and PIE, full PoC and reliability.
- A firmware image with a root shell and an update weakness.
- A mobile app with an exported component and a data leak.
- An LLM app with an indirect injection and a tool abuse.

A capstone needs a report, a reproduction script, a five-minute talk, and a peer review.

## Spaced repetition

Keep a deck of 20 prompts from old labs. Review it three times a week. Prompts look like:

- What does full RELRO protect, and what does it not?
- Which header does a cache key include?
- How do you tell a controlled RIP from a stack pointer?
- What is the difference between BOLA and BFLA?
- Which signing step stops a firmware downgrade?

If you cannot answer in 20 seconds, reopen the lab.

## Notes

One file per week under `training/notes`. Each file has:

```text
Date:
Target:
Hypothesis:
Commands:
Result:
Evidence:
What surprised me:
What I would do differently:
Next:
```

Link the evidence file. A note without a path is a diary.

## Self-training with the agent

1. Ask Beril to explain the week's concept, then close the answer and rewrite it.
2. Ask for a lab hint, not the answer. Use `agents/` prompts for structure.
3. After a finding, ask for a critique against `references/scoring.md`.
4. Add a method card when a lab teaches something transferable.
5. Update the lab README when a step is wrong.

The agent is a tutor and a reviewer. It is not a substitute for typing the commands.

## Ethics

- Test only what is in scope.
- Stop at the agreed proof.
- Do not run exploits against production without a rollback plan.
- Credit the source of every technique.
- Keep customer data out of public writeups.

## Progress commands

```bash
./beril train --status
./beril train --week 7
./beril train --next
./beril train --done w7d1
./beril train --log "IDOR reproduced with two accounts"
```

The state lives in `training/progress.json`, which is gitignored. Commit your notes and writeups separately.
