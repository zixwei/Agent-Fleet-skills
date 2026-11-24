---
name: kubernetes-security
description: Assess authorized Kubernetes and managed clusters for API server, identity, RBAC, workload, node, network, storage, supply-chain and audit configuration weaknesses using read-only checks.
---

# Kubernetes security assessment

## Purpose

Review Kubernetes clusters, control-plane settings, node configuration, workloads, cluster add-ons and managed-service integrations. Include upstream Kubernetes, OpenShift and managed EKS, AKS and GKE environments where explicitly authorized.

## Prerequisites

- Written authorization identifies cluster names/IDs, cloud account/project, namespaces, node pools, permitted API collection and test window.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Use a dedicated audit identity with read-only RBAC. The identity must not create pods, bind roles, read secret values or exec into workloads.
- Confirm whether node/host configuration, managed control-plane configuration and cloud identity integrations are in scope.
- Schedule collection with cluster owners; broad API listing can create load and disclose sensitive resource metadata.

## Tools

- **kube-bench:** evaluate node and control-plane configuration against CIS Kubernetes Benchmark. [Project](https://github.com/aquasecurity/kube-bench)
- **Kubescape:** cluster and workload posture assessment. [Documentation](https://kubescape.io/)
- **kubeaudit:** inspect common Kubernetes security configuration. [Project](https://github.com/Shopify/kubeaudit)
- **Polaris:** validate workload and Helm configuration. [Documentation](https://polaris.docs.fairwinds.com/)
- **Kyverno:** inspect admission policy coverage and policy reports. [Documentation](https://kyverno.io/docs/)
- **Falco:** review existing runtime detection configuration and alerts; do not deploy or change rules during assessment. [Documentation](https://falco.org/docs/)

## Workflow

### 1. Establish cluster identity and versions

Record cluster ID, Kubernetes version, distribution, API endpoint identity, node operating systems, managed-service version, enabled add-ons and namespace boundary. Verify that the current kube-context is the exact approved cluster before every collection.

### 2. Review API server and control-plane configuration

Review authentication methods, anonymous access, authorization mode, admission controllers, audit policy, encryption-at-rest configuration, API endpoint exposure, TLS settings and control-plane logging. For managed services, record provider-controlled settings that cannot be independently inspected.

### 3. Review identity and RBAC

Inspect service-account usage, cluster-admin bindings, wildcard permissions, role aggregation, namespace boundaries, impersonation permissions, token automounting, workload identity and stale bindings. Avoid reading Secret objects or using another workload's service-account token.

### 4. Review pod and workload security

Review Pod Security Standards, security contexts, runAsNonRoot, privilege escalation settings, capabilities, seccomp, AppArmor/SELinux, host namespaces, hostPath mounts, privileged mode, read-only root filesystems, resource limits, image digests and pull policies. Use manifest and admission-policy analysis before inspecting live objects.

### 5. Review network and storage boundaries

Inspect NetworkPolicies, ingress/egress controls, service types, load balancers, node ports, service meshes, DNS, storage classes, persistent volumes, CSI driver permissions, encryption and backup access. Do not probe service endpoints beyond the approved scope.

### 6. Review nodes and runtime

Assess node patch/support posture, kubelet configuration, container runtime, node SSH/management exposure, metadata access, kernel hardening, audit logs and runtime monitoring. Use provider-supported node posture tools where node access is not permitted.

### 7. Review supply chain and cluster lifecycle

Inspect image registries and digests, SBOM and signature enforcement, admission policy, Helm chart sources, operator privileges, GitOps credentials, upgrade support, etcd backup protection and control-plane recovery. Review configurations and attestation metadata; do not deploy test workloads.

### 8. Compare against the right benchmark

Choose the exact benchmark for the distribution and managed service (upstream Kubernetes, EKS, AKS, GKE, OpenShift or other). CIS Kubernetes Benchmark 2.1.0 and the provider-specific benchmarks may differ; do not apply upstream control expectations to a managed control plane that the customer cannot configure.

### 9. Validate and report

Record cluster/version, namespace, resource kind/name, rule ID, expected control, actual configuration, provider ownership, evidence and remediation. Redact Secret data and service-account tokens.

## Output

A cluster inventory, API/control-plane findings, RBAC map, workload hardening results, network/storage review, node/runtime observations, supply-chain findings, managed-service limitations and prioritized remediation.

## Safety constraints

- Read-only API access. Do not create pods, change RBAC, alter admission settings or read secret payloads.
- Do not deploy privileged workloads, run containers on nodes, test escape paths or access host files.
- Do not run stress tests, destructive probes or cluster-wide mutations.
- Stop if the kube-context changes, the audit identity has unexpected write access or workload stability is affected.

## References

- [CIS Kubernetes Benchmarks](https://www.cisecurity.org/benchmark/kubernetes)
- [kube-bench](https://github.com/aquasecurity/kube-bench)
- [Kubescape](https://kubescape.io/)
- [Kubernetes Security Checklist](https://kubernetes.io/docs/concepts/security/)
- [Kubernetes Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)
