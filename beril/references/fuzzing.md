# Fuzzing

Fuzzing is automated input generation with a feedback signal. It finds crashes, not bugs. A crash becomes a finding only after triage, root cause, and impact.

Use it when the target has a parser, a protocol, or a file format and you can run it locally in a lab.

## Fuzzing ladder

| Rung | Method | When |
|---|---|---|
| 0 | Hand-written test cases | Small input space, known grammar |
| 1 | Dumb mutation | Quick smoke test, no instrumentation |
| 2 | Coverage-guided, file input | Parser or file format with source or a harness |
| 3 | Structure-aware | Length fields, checksums, compression, TLS |
| 4 | Snapshot fuzzing | Large stateful target, slow init, closed source |
| 5 | Kernel or hypervisor | Syscalls, drivers, IOCTL surfaces |

Do not start at rung 5 because a blog post did. A file parser with a good harness at rung 2 finds more in a day than a badly configured kernel fuzzer finds in a week.

## Harness first

The harness is the product. A bad harness fuzzes the runtime, not the parser.

### LibFuzzer harness

```c
#include <stdint.h>
#include <stddef.h>
#include "parser.h"

int LLVMFuzzerTestOneInput(const uint8_t *data, size_t size) {
    if (size < 8 || size > 1024 * 1024) return 0;
    parser_ctx *ctx = parser_new();
    parser_feed(ctx, data, size);
    parser_free(ctx);
    return 0;
}
```

```bash
clang -g -O1 -fsanitize=fuzzer,address,undefined harness.c parser.c -o fuzz_parser
./fuzz_parser corpus/ -max_len=1048576 -jobs=4 -workers=4
```

`-jobs` and `-workers` run parallel processes. `-dict` loads a token dictionary. `-rss_limit_mb` stops a leak from eating the box.

Rules:

- Reset state between runs.
- Free everything the parser allocates.
- Do not print in the hot path.
- Cap input size.
- Seed with valid files.

### AFL++ harness

File input:

```c
#include <stdio.h>
#include <stdlib.h>
int main(int argc, char **argv) {
    FILE *f = fopen(argv[1], "rb");
    fseek(f, 0, SEEK_END); long n = ftell(f); rewind(f);
    unsigned char *buf = malloc(n);
    fread(buf, 1, n, f); fclose(f);
    parse(buf, n);
    free(buf);
    return 0;
}
```

Persistent mode is much faster:

```c
__AFL_FUZZ_INIT();
int main(void) {
    __AFL_INIT();
    unsigned char *buf = __AFL_FUZZ_TESTCASE_BUF;
    while (__AFL_LOOP(10000)) {
        int len = __AFL_FUZZ_TESTCASE_LEN;
        parse(buf, len);
    }
    return 0;
}
```

Build:

```bash
afl-clang-fast -g -O1 harness.c parser.c -o fuzz_parser
```

Run:

```bash
mkdir -p in out
cp seeds/* in/
afl-fuzz -i in -o out -m none -t 1000+ -- ./fuzz_parser @@
```

`-m none` removes the memory limit for ASAN builds. `-t 1000+` allows a slow first run to calibrate.

### Network harness

Do not fuzz through a real socket stack if you can avoid it. Extract the handler and feed a buffer. When you must use a socket, put the server in a loop and reset state per input.

```python
import socket
def run(data):
    s = socket.create_connection(("127.0.0.1", 9000))
    s.sendall(data)
    s.shutdown(socket.SHUT_WR)
    while s.recv(4096):
        pass
```

## Grammar and structure

Mutating a valid file breaks the checksum at byte 4 and the parser stops early.

Tools and methods:

- **AFL++ dictionaries** for magic values and keywords: `-x dict.txt`.
- **Custom mutator** in C or Python for length, checksum, and chunk order.
- **libprotobuf-mutator** for protobuf.
- **TLS-Attacker** or **tlspuffin** for TLS state machines.
- **Boofuzz** for network protocols with a block model.
- **Grammar inference** from a corpus, then generate valid seeds.

Fixing the checksum after mutation is often the difference between 2 percent and 80 percent coverage.

## Corpus

- Start with the smallest valid files from the test suite.
- Add real samples, one per format variant.
- Minimize with `afl-cmin` and `afl-tmin`.
- Keep the corpus small. A bloated corpus slows every cycle.
- Store interesting inputs from other fuzzers. Ensemble fuzzing shares coverage.

```bash
afl-cmin -i corpus -o corpus_min -- ./fuzz_parser @@
afl-tmin -i crash -o crash_min -- ./fuzz_parser @@
```

## Sanitizers and coverage

```bash
export AFL_USE_ASAN=1
export AFL_USE_UBSAN=1
export AFL_LLVM_CMPLOG=1
```

CMPLOG helps with magic comparisons. Redqueen and input-to-state transforms turn a string compare into a guided mutation.

Coverage:

```bash
afl-showmap -o map -- ./fuzz_parser seed
llvm-cov show ./fuzz_parser -instr-profile=default.profraw
```

A campaign that stops finding new edges is stalled. Change the dictionary, the seed, the mutator, or the target function.

## Parallel and continuous

```bash
afl-fuzz -M main -i in -o sync -- ./fuzz_parser @@
afl-fuzz -S secondary1 -i in -o sync -- ./fuzz_parser @@
```

CI with a time box and a crash budget:

```yaml
- name: fuzz
  run: |
    ./fuzz_parser corpus/ -max_total_time=600 -artifact_prefix=artifacts/
```

A pull request that adds a parser gets 10 minutes of fuzzing. A nightly job gets hours.

## Snapshot fuzzing

For a large target with slow initialization, snapshot the state and restore per input.

- **Nyx** with the AFL++-Nyx fork.
- **Snapchange** for Linux kernel and full-system snapshots.
- **wtf** for Windows kernel.

```bash
NYX_MODE=1 AFL_MAP_SIZE=1048576 afl-fuzz -i seeds -o out -- ./target_nyx @@
```

Snapshot setup is a project, not an afternoon. Use it when init takes minutes or the target is closed source.

## Kernel and syscall fuzzing

`syzkaller` is the default for Linux syscalls.

```bash
make
./bin/syz-manager -config manager.cfg
```

The config names the kernel image, SSH target, and corpus. Start with a QEMU image and a small syscall set. A broad syscall description on a custom kernel produces noise.

For Windows drivers, use **kAFL**, **wtf**, or **Driver Verifier** plus a hand-written IOCTL harness.

## Crash triage

1. Reproduce outside the fuzzer.
2. Deduplicate by stack and fault type.
3. Minimize.
4. Classify: OOB, UAF, integer, null, assertion.
5. Check exploitability with `vulnerability-research.md`.
6. Keep the input and the build hash.

```bash
casr-afl -i out/default/crashes -o reports -- ./fuzz_parser @@
casr-cluster -d reports dedup
casr-cluster -c dedup clusters
```

## Pitfalls

| Pitfall | Result | Fix |
|---|---|---|
| Harness leaks state | False crashes and slowdown | Reset and free per input |
| No size cap | Huge inputs, slow cycles | `-max_len`, early return |
| Checksum not fixed | Shallow coverage | Custom mutator |
| ASAN without `-m none` | OOM kills | Remove the memory limit |
| Fuzzing a release build | Optimized-away checks | `-O1 -g`, no LTO |
| Corpus of one file | Local maximum | Add format variants |
| Running on the host | Crash loses state | Container or VM with snapshot |
| No crash dedup | Same bug 200 times | CASR or stack hash |

## When to stop

- The time box ends.
- New edges per hour approach zero and mutation changes do not help.
- The parser surface is covered and the remaining code needs a different harness.
- You have enough crashes to triage for the report.

Write down the target build, harness, corpus, fuzzer version, flags, and run time. A fuzzing result without that context is not reproducible.

## Sources

- AFL++ documentation and `docs/fuzzing_in_depth.md`
- libFuzzer documentation
- `SnailSploit/Claude-Red` fuzzing and crash analysis skills
- `trailofbits/skills` fuzzing skills
- CASR documentation
- syzkaller documentation
- unblob fuzzing setup as a real harness example
