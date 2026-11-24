# Language notes for focused checks

Use the repository's scripts and CI configuration first. These examples apply when the named tool is already installed and fits the project.

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

For a focused verification, add a temporary test next to existing tests so it exercises the real module through the project's runner, then delete it. If infrastructure is unavailable, use a smaller check that still supports the claim and report what remains unverified.
