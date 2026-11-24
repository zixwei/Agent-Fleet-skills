---
name: cicd-supply-chain-security
description: Assess authorized source repositories, CI/CD workflows, build runners, artifacts, dependencies, SBOMs and signing/provenance controls for software supply-chain security weaknesses.
---

# CI/CD and software supply-chain security assessment

## Purpose

Review the path from source commit to released artifact. Cover repository access, workflow permissions, runner isolation, dependency resolution, build secrets, artifact integrity, signing, provenance and release controls.

## Prerequisites

- Written authorization identifies repositories, organizations/projects, CI providers, runner pools, registries, artifact stores and release workflows.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Use read-only repository and CI identities. Confirm whether private source and build logs may be copied into the assessment environment.
- Use non-production test branches and synthetic secrets for any active workflow validation.
- Do not trigger releases, deploy workflows or modify repository/organization settings.

## Tools and references

- **Gitleaks:** detect committed secrets locally. [Project](https://github.com/gitleaks/gitleaks)
- **Semgrep:** source and workflow analysis. [Docs](https://semgrep.dev/docs/)
- **Trivy:** dependency, filesystem, image and IaC analysis. [Docs](https://trivy.dev/latest/)
- **Syft / Grype:** SBOM generation and package vulnerability matching. [Syft](https://github.com/anchore/syft) · [Grype](https://github.com/anchore/grype)
- **Checkov:** IaC and cloud policy analysis. [Docs](https://www.checkov.io/1.Welcome/What%20is%20Checkov.html)
- **zizmor:** GitHub Actions workflow static analysis. [Project](https://github.com/woodruffw/zizmor)
- **OpenSSF Scorecard:** assess open-source repository supply-chain practices. [Docs](https://scorecard.dev/)
- **Cosign:** verify signatures and attestations. [Docs](https://docs.sigstore.dev/cosign/overview/)
- **SLSA:** build integrity and provenance framework. [Specification](https://slsa.dev/spec/v1.1/)

## Workflow

### 1. Map source and release boundaries

Inventory repositories, owners, branch protection, CODEOWNERS, protected environments, forks, build triggers, release tags, package registries and artifact destinations. Identify external contributors and trusted automation identities.

### 2. Review repository access

Inspect organization/repository roles, write permissions, deploy keys, GitHub Apps, OAuth applications, bot tokens, branch protection, required reviews, signed commits/tags and secret-scanning settings. Do not retrieve secret values.

### 3. Review workflow definitions and triggers

Inspect pull_request, pull_request_target, workflow_run, issue_comment, release, tag and dispatch triggers. Review permissions blocks, token scope, untrusted fork input handling, third-party actions pinned to immutable commit SHAs, reusable workflows, artifact trust and environment approval. Examine expression interpolation and command construction as source text only.

### 4. Review runner isolation

Assess ephemeral versus persistent runners, job isolation, privileged containers, self-hosted runner network access, workspace cleanup, cache/artifact sharing, egress controls and credential exposure. Do not execute untrusted workflow code on production runner hosts.

### 5. Review dependency and build integrity

Inspect lockfiles, package sources, pinned versions, transitive dependencies, registries, Git submodules, build plugins, compiler/toolchain versions, container base images, build scripts and reproducibility. Check for typosquatting indicators, abandoned packages, unsigned artifacts and dependency drift.

### 6. Review secrets and signing

Review secret scope, environment protection, OIDC federation, short-lived credentials, signing identities, key management, certificate expiry and release approval. Use metadata and policy views; never print, copy or test actual secret values.

### 7. Review SBOM and provenance

Check SBOM formats/coverage, artifact digest linkage, provenance attestations, builder identity, source commit binding, signing verification, release metadata and vulnerability policy. Verify that the deployed artifact digest matches the approved build output.

### 8. Review deployment controls

Inspect protected environments, approvals, deployment identities, IaC plan/apply separation, drift detection, rollback, audit logs and production change controls. Do not run a production deployment or change infra state.

### 9. Validate and report

Use static analysis and read-only APIs. Active checks should use a non-production test branch with synthetic credentials and owner approval. Record workflow path/line, action commit, effective permissions, runner class, artifact digest and evidence source.

## Output

Repository and CI inventory, access-control review, workflow trigger/permission findings, runner isolation report, dependency/SBOM findings, signing/provenance assessment and prioritized remediation.

## Safety constraints

- Never steal, reveal or validate real secrets by attempting login.
- Do not poison packages, publish artifacts, trigger releases, tamper with caches or exploit production runners.
- Do not run attacker-controlled workflow code or modify branch protections.
- Stop if a workflow could deploy or mutate production infrastructure as an unintended side effect.

## References

- [SLSA Specification](https://slsa.dev/spec/v1.1/)
- [OpenSSF Scorecard](https://scorecard.dev/)
- [Sigstore Cosign](https://docs.sigstore.dev/cosign/overview/)
- [GitHub Actions Security Hardening](https://docs.github.com/actions/security-guides/security-hardening-for-github-actions)
- [zizmor](https://github.com/woodruffw/zizmor)
- [OWASP SCVS](https://owasp.org/www-project-software-component-verification-standard/)
