---
name: cloud-security-assessment
description: Assess explicitly authorized AWS, Microsoft Azure and Google Cloud environments for IAM, network, storage, encryption, logging, workload, key-management and governance misconfigurations using provider-supported read-only checks.
---

# Cloud security assessment

## Purpose

Review cloud accounts, subscriptions and projects that are explicitly included in the engagement. The assessment is posture and configuration focused across AWS, Azure and Google Cloud. Use provider-native read-only roles and approved inventory snapshots.

## Prerequisites

- Written authorization identifies the cloud tenant/account/subscription/project IDs, regions, services, test window and approved assessment identity.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- The owner provisions a dedicated read-only audit identity or role with least privilege.
- Confirm allowed regions, excluded accounts/projects, managed-service dependencies, data residency constraints and whether security findings may be exported.
- Do not use owner/root credentials, long-lived user keys or credentials from another engagement.

## Tooling

- **Prowler:** multi-cloud security posture and compliance checks. [Project](https://github.com/prowler-cloud/prowler) · [Provider registry](https://registry.prowler.com/providers)
- **ScoutSuite:** multi-cloud security auditing. [Project](https://github.com/nccgroup/ScoutSuite)
- **CloudSplaining:** AWS IAM policy risk analysis. [Project](https://github.com/salesforce/cloudsplaining)
- **AWS:** IAM Access Analyzer, Security Hub, Config, CloudTrail and Well-Architected Security Pillar. [AWS security guidance](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/)
- **Azure:** Defender for Cloud, Azure Policy, Entra ID and Resource Graph. [Azure security documentation](https://learn.microsoft.com/azure/security/)
- **Google Cloud:** Security Command Center, IAM Recommender, Policy Intelligence, Cloud Asset Inventory and audit logs. [Google Cloud security](https://cloud.google.com/security)

Use the exact tool/provider release and profile supported by the target cloud. Review required API permissions before granting them.

## Workflow

### 1. Establish the cloud boundary

Build an inventory of approved accounts/subscriptions/projects, regions, organizational units, linked tenants, managed services, workloads and network boundaries. Verify identifiers with the cloud owner. Do not discover or inspect sibling tenants or accounts.

### 2. Review identities and IAM

Inspect root/owner access, privileged roles, service identities, workload identities, trust relationships, role assumptions, access keys, credential age, MFA, conditional access, break-glass accounts and unused permissions. Review effective permissions and wildcards; prefer policy simulation and provider recommendations. Do not assume or obtain identities that were not provisioned for the assessment.

### 3. Review network exposure

Assess VPC/VNet networks, security groups/NSGs, firewall rules, public IPs, load balancers, peering, transit gateways, private endpoints, DNS, VPN and administrative access. Verify internet exposure only for explicitly in-scope services. Do not send traffic to third-party or tenant-external endpoints.

### 4. Review storage and data protection

Inspect public access settings, bucket/container/object policies, database exposure, backup access, encryption at rest, TLS, key rotation, key policies, retention and audit trails. Never download customer objects or enumerate content. Use metadata and configuration only.

### 5. Review workload and platform configuration

Assess VM images, instance metadata settings, container registries, serverless functions, managed Kubernetes, PaaS configuration, secrets managers, patch posture, workload identity and isolation controls. Do not execute code in cloud workloads or alter deployments.

### 6. Review logging, detection and governance

Check CloudTrail/Activity Logs/Audit Logs coverage, organization-level logging, retention, immutable storage, alerting, security-posture services, configuration drift, policy guardrails, region restrictions and incident response integration.

### 7. Prioritize findings

Map observations to provider security guidance, CIS cloud foundations benchmarks and applicable organizational controls. Distinguish a policy warning from a reachable exposure or confirmed access path. Record resource identifiers only where permitted; redact account IDs and sensitive metadata in shared evidence.

### 8. Validate and report

For each finding, include provider, account/project alias, region, resource type, exact configuration, evidence source, permission context, business impact, remediation and owner. Do not change IAM or network policy during assessment.

## Provider-specific focus

- **AWS:** Organizations and SCPs, IAM trust and policies, S3 public access, KMS, CloudTrail, Config, EC2 security groups, VPC endpoints, ECR, EKS and Lambda.
- **Azure:** Management Group/subscription structure, Entra roles, managed identities, Key Vault, Storage Accounts, NSGs, Defender for Cloud, Azure Policy, AKS and diagnostic settings.
- **Google Cloud:** organization/folder/project hierarchy, IAM bindings, service accounts, Cloud Storage, KMS, firewall rules, Cloud Logging, SCC, GKE and workload identity federation.

## Output

A cloud asset inventory, IAM review, network exposure map, storage/key protection findings, workload posture, audit coverage, policy deviations, provider-specific evidence and prioritized remediation.

## Safety constraints

- Use read-only roles. Do not modify policies, create identities, rotate keys, disable logging or deploy resources.
- Do not access object contents, secrets, customer records or workload memory.
- Do not enumerate external organizations or expand scope through trust relationships.
- Stop if an API call returns resources outside the approved tenant or the audit identity has broader permissions than expected.

## References

- [Prowler](https://github.com/prowler-cloud/prowler)
- [AWS Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/)
- [Microsoft Azure Security](https://learn.microsoft.com/azure/security/)
- [Google Cloud Security](https://cloud.google.com/security)
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
