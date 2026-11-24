---
name: database-security-assessment
description: Assess authorized PostgreSQL, MySQL/MariaDB, Microsoft SQL Server, MongoDB, Redis and Elasticsearch deployments for authentication, authorization, network, encryption, auditing, backup and data-protection weaknesses.
---

# Database security assessment

## Purpose

Review relational and NoSQL database systems, managed database services and their administration interfaces. Focus on configuration, identity, network exposure, encryption, logging, backup and data minimization.

## Prerequisites

- Written authorization identifies database instances, clusters, environments, endpoints, schemas if in scope, permitted audit queries and test window.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- The database owner provisions a read-only audit role with metadata-only access where possible.
- Do not use application or DBA credentials unless explicitly authorized. Never query production row contents unless a separate data-access approval exists.
- Confirm backup, replication, failover and maintenance restrictions.

## Tools

- Vendor-native metadata/system catalog views and audit facilities.
- **pgAudit:** PostgreSQL audit logging. [Project](https://www.pgaudit.org/)
- **MySQL Enterprise Audit / MariaDB Audit Plugin:** review installed vendor-supported audit capabilities. [MySQL](https://dev.mysql.com/doc/refman/8.4/en/audit-log.html) · [MariaDB](https://mariadb.com/kb/en/mariadb-audit-plugin/)
- **SQL Server Audit:** inspect audit specifications and retention. [Documentation](https://learn.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)
- **MongoDB Security Checklist:** review authentication, RBAC, TLS and network binding. [Documentation](https://www.mongodb.com/docs/manual/administration/security-checklist/)
- **Redis security:** review protected mode, ACLs, TLS and network exposure. [Documentation](https://redis.io/docs/latest/operate/oss_and_stack/management/security/)
- **Trivy / Semgrep:** review database images, IaC and application data access patterns. [Trivy](https://trivy.dev/latest/) · [Semgrep](https://semgrep.dev/docs/)

## Workflow

### 1. Inventory engines and topology

Record engine/version, edition, support status, cluster topology, primary/replica roles, managed-service provider, network placement, client applications, backup destinations and administrative interfaces.

### 2. Review authentication and authorization

Inspect authentication methods, password policy, MFA/SSO integration, role hierarchy, default accounts, dormant accounts, service identities, superuser/owner roles, grants, database/schema/table privileges and row-level security. Use metadata queries only. Do not attempt password guessing or privilege escalation.

### 3. Review network exposure

Assess bind/listen addresses, firewall rules, private endpoints, TLS, client certificate options, management ports, replication channels and administrative access sources. Verify only approved endpoints; do not enumerate adjacent network services.

### 4. Review encryption and secrets

Review encryption in transit, encryption at rest, KMS/HSM integration, key rotation, secret manager usage, connection-string handling, client certificate protection and backup encryption. Never retrieve or reveal key material or secret values.

### 5. Review dangerous features and configuration

- PostgreSQL: extensions, SECURITY DEFINER routines, search_path, replication slots, pg_hba.conf and superuser boundaries.
- MySQL/MariaDB: authentication plugins, grants, TLS, local file import/export settings, event scheduler and plugin loading policy.
- SQL Server: server roles, linked servers, CLR/external scripts, xp_cmdshell configuration, SQL Agent ownership and database trust settings.
- MongoDB: authorization mode, bind address, TLS, replica-set security, roles, JavaScript/server-side features and audit settings.
- Redis: protected mode, ACLs, command exposure, persistence files, module loading and replication trust.
- Elasticsearch: security/authentication, transport TLS, role mappings, snapshots, cross-cluster access and index exposure.

Review configuration; do not activate dangerous features or execute operating-system commands.

### 6. Review data protection and lifecycle

Inspect backup encryption/retention, restore testing, point-in-time recovery, deletion policies, data masking, test data generation, audit retention, replica consistency and access to snapshots. Do not export data rows or inspect actual customer values.

### 7. Review application query safety

Review application code for parameterized queries, ORM usage, stored procedure boundaries, query construction, multi-tenant filters and authorization checks. Use local fixtures or a designated test schema for dynamic validation. Delegate application-layer injection testing to the web/API skills.

### 8. Validate and report

Record engine/version, configuration source, database role used, metadata query name (not sensitive output), expected control, observed state, scope and service impact. Map to CIS/vendor guidance and propose owner-reviewed changes.

## Output

Database inventory/topology, role and grant review, network/TLS posture, encryption/key management, engine-specific configuration findings, audit/backup review and prioritized remediation.

## Safety constraints

- Metadata-only by default. Do not read, dump, export or alter production records.
- Do not alter users, roles, grants, schema, data, extensions, replication, audit policy or server settings.
- No destructive SQL, bulk queries, load testing, command execution features or credential guessing.
- Stop if the audit identity can read sensitive row data unexpectedly or replication/availability is affected.

## References

- [PostgreSQL Security](https://www.postgresql.org/docs/current/security.html)
- [pgAudit](https://www.pgaudit.org/)
- [MySQL 8.4 Audit Log](https://dev.mysql.com/doc/refman/8.4/en/audit-log.html)
- [SQL Server Audit](https://learn.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)
- [MongoDB Security Checklist](https://www.mongodb.com/docs/manual/administration/security-checklist/)
- [Redis Security](https://redis.io/docs/latest/operate/oss_and_stack/management/security/)
