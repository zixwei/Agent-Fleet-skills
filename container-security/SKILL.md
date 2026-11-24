---
name: container-security
description: Assess authorized container images, registries and runtimes for vulnerable packages, insecure build provenance, excessive privileges, secret exposure, filesystem and network misconfiguration using offline or read-only analysis.
---

# Container security assessment

## Purpose

Review container images, registries, build pipelines and runtime configurations. Include Docker, Podman, OCI-compatible images and container workloads running on virtual machines or managed platforms.

## Prerequisites

- Authorization identifies the image repositories, tags/digests, registry projects, host/runtime fleet and permitted data collection.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Obtain immutable image digests and approved manifests from the owner.
- Confirm whether images may be pulled to an isolated analysis host and whether source code, layers or SBOMs are confidential.
- Use read-only registry credentials. Do not run untrusted entrypoints or startup scripts.

## Tools

- **Trivy:** image, filesystem, secret, misconfiguration and SBOM analysis. [Documentation](https://trivy.dev/latest/)
- **Syft:** generate SBOMs from images and filesystems. [Documentation](https://github.com/anchore/syft)
- **Grype:** package vulnerability matching. [Documentation](https://github.com/anchore/grype)
- **Dockle:** container image configuration checks. [Documentation](https://github.com/goodwithtech/dockle)
- **Docker Bench for Security:** read-only Docker host configuration checks where supported. [Project](https://github.com/docker/docker-bench-security)
- **CIS Docker Benchmark:** use the version aligned to the deployed Docker release. [CIS catalog](https://www.cisecurity.org/cis-benchmarks)

## Workflow

### 1. Inventory images and provenance

Record registry, repository, tag, immutable digest, base image, build source, builder identity, signature/attestation, SBOM and deployment environments. Identify mutable tags, unsupported bases, untrusted build contexts and unverified third-party layers.

### 2. Scan images and dependencies

Run approved offline or registry-read-only analysis against the pinned digest. Review OS packages, application dependencies, known vulnerabilities, fixed versions, end-of-life packages, malware indicators and licenses. Confirm whether a vulnerable package is reachable and present in the final runtime image.

### 3. Review secrets and build artifacts

Inspect image metadata and authorized layers for accidentally embedded tokens, private keys, certificates, environment files, build caches and development artifacts. Do not extract or use discovered secrets; notify the owner for rotation. Redact values from output.

### 4. Review image hardening

Assess non-root execution, user namespace behavior, read-only root filesystem, minimal base image, unnecessary packages, capabilities, seccomp, AppArmor/SELinux, health checks, exposed ports, default command, shell/debug tools and filesystem permissions. Avoid running the container to test these settings.

### 5. Review runtime and host configuration

Inspect Docker/Podman daemon configuration, rootless mode, socket exposure, privileged containers, host PID/network namespaces, hostPath/bind mounts, device access, resource limits, logging, network segmentation and runtime monitoring. Review only the hosts or clusters in scope.

### 6. Review registry and supply-chain controls

Assess private registry access, MFA, token lifetime, robot accounts, immutable tags, image signing, provenance attestations, vulnerability gates, retention, replication and deletion controls. Confirm that CI publishes the same digest that is deployed.

### 7. Validate and report

Compare each issue with CIS and vendor guidance. Distinguish image-level vulnerability findings from host/runtime configuration risks. Record digest, scanner/database versions, scan options, package path, fixed version, policy deviation and false-positive rationale.

## Output

An image inventory, SBOM references, vulnerability register, image-hardening review, runtime/host configuration findings, registry/supply-chain observations and remediation recommendations.

## Safety constraints

- Prefer static analysis and read-only registry access. Never run an unknown image or its entrypoint on the analyst workstation.
- Do not enter privileged containers, access host namespaces, dump container secrets or test escape paths against production.
- Do not delete, retag, rebuild or redeploy images during assessment.
- Store SBOMs and scan output as potentially sensitive artifacts.

## References

- [Trivy](https://trivy.dev/latest/)
- [Syft](https://github.com/anchore/syft)
- [Grype](https://github.com/anchore/grype)
- [Dockle](https://github.com/goodwithtech/dockle)
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
