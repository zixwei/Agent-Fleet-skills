# Language notes for focused checks

Follow **Host checks** or **Isolated checks** in [the skill](../SKILL.md#3-verify-findings-and-run-safe-checks). Use commands from the trusted baseline or the user. These examples apply when the named tool is already available and fits the project.

For host checks, reuse trusted baseline dependencies only when the dependency manifests and lockfiles match. Copy `node_modules` into the snapshot or run the baseline virtualenv's interpreter from the snapshot and verify project imports resolve there. For changed dependencies, use a compatible environment for the reviewed revision. See [Isolated checks](isolated-checks.md) for offline provisioning.

| Language | Build, type, or lint checks | Focused test |
|---|---|---|
| JavaScript / TypeScript | `tsc --noEmit`, `eslint <files>` | `vitest run <file>` or `jest <file>` |
| Python | `mypy <package>`, `ruff check <files>` | `pytest <file> -k <name>` |
| Go | `go vet ./<package>` | `go test ./<package> -run <Name>` |
| Java | `mvn compile` or `gradle compileJava` | `mvn -Dtest=<Class>#<method> test` or `gradle test --tests <Class>` |
| .NET | `dotnet build` | `dotnet test --filter <Name>` |
| Rust | `cargo check`, `cargo clippy` | `cargo test <name>` |
| Ruby | `rubocop <files>` | `rspec <file>:<line>` |
| PHP | `phpstan analyse <files>`, `phpcs <files>` | `phpunit --filter <name>` |

For a focused verification, add a temporary test alongside existing tests in the disposable snapshot so it exercises the real module through the project's runner. If infrastructure is unavailable, use a smaller check where it can support the claim and report what remains unverified.
