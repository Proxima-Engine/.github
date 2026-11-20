# AGATA PROXIMA

## Database security infrastructure for multi-tenant applications.

**Make tenant identity enforceable in the database connection path.**

[Website](https://agataproxima.com) · [Public Engine](https://github.com/Proxima-Engine/proxima-engine) · [Documentation](https://github.com/Proxima-Engine/proxima-engine/tree/main/docs) · [Discord](https://discord.gg/xsYSuDMzg)

---

Agata Proxima is a database security platform for applications that serve multiple customer organizations. Proxima Engine sits between the application and its database, authenticates database sessions, binds each session to an authorized tenant identity and applies the controls appropriate to the selected database.

The platform is designed to expand across database systems. Its current Engine implementations cover PostgreSQL, MySQL and MariaDB. Each adapter keeps its own protocol behavior, identity mapping, upstream credentials, native permission model and real-database verification suite.

## Platform architecture

Agata Proxima uses two cooperating planes:

- **Proxima Engine — data plane.** Accepts application database connections, authenticates clients, binds sessions to tenant identities, applies database-specific enforcement and connects to the configured upstream database.
- **Agata Proxima Cloud — management plane.** Provides organization-scoped management for tenants, policies, Engine nodes, deployment workflows, verification records and audit evidence.

The Cloud management channel is separate from application SQL traffic. Customer-operated Engine nodes remain in the application-to-database path. Cloud coordinates management and records operational state; it does not become a database proxy.

~~~mermaid
flowchart TB
    App["Trusted application backend<br/>authenticates user + resolves tenant"]
    subgraph DataPlane["Customer-controlled runtime"]
      PGDriver["PostgreSQL driver"]
      MyDriver["MySQL driver"]
      Engine["Proxima Engine<br/>Rust data plane"]
      PG[("PostgreSQL<br/>roles, grants, RLS")]
      MySQL[("MySQL")]
      Maria[("MariaDB")]
      App --> PGDriver -->|"PostgreSQL protocol"| Engine
      App --> MyDriver -->|"MySQL-compatible protocol"| Engine
      Engine --> PG
      Engine --> MySQL
      Engine --> Maria
    end
    Cloud["Agata Proxima Cloud<br/>organization + Engine management"]
    Cloud -. "secure enrollment, management and evidence" .-> Engine
~~~

The trusted application backend remains responsible for user authentication and tenant authorization. A tenant identifier supplied by an untrusted client is not authorization proof. The Engine enforces the configured connection identity; the upstream database applies its native permissions.

## Current database integrations

| Database | Protocol path | Tenant enforcement model | Guide |
|---|---|---|---|
| **PostgreSQL** | Native PostgreSQL wire-protocol listener | Signed tenant context, tenant-to-role routing, least-privilege roles, grants and Row-Level Security | [PostgreSQL guide](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/POSTGRESQL.md) |
| **MySQL** | MySQL-compatible gateway | Authenticated client identity, immutable tenant session, assigned database validation and tenant-specific upstream account/grants | [MySQL guide](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/MYSQL.md) |
| **MariaDB** | MySQL-compatible gateway | Authenticated client identity, immutable tenant session, assigned database validation and tenant-specific upstream account/grants | [MariaDB guide](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/MARIADB.md) |

MySQL and MariaDB share the client protocol gateway but remain separate database targets with independent setup and real-database verification. A passing MySQL test is not proof of MariaDB isolation, and a PostgreSQL RLS result is not proof of either MySQL-compatible target.

### PostgreSQL

The PostgreSQL adapter verifies signed tenant context, maps the verified tenant to its configured upstream role and works with PostgreSQL grants and RLS. The reference quickstart includes a local Compose environment, driver examples, PgBouncer transaction-pooling coverage, negative access tests and recovery checks.

### MySQL and MariaDB

The MySQL-compatible gateway authenticates clients against a protected identity registry, binds each session to one tenant principal, validates the assigned upstream database and connects using tenant-specific least-privilege credentials. MySQL and MariaDB have separate server configuration, compatibility targets and integration suites.

### Additional database systems

The platform is intended to expand through explicit database adapters. A database is described as supported only when its protocol implementation, identity model, native permission strategy, configuration, documentation and real-database acceptance suite are present. Roadmap work is not equivalent to production support.

## Security model

- **Trusted tenant resolution:** the application backend resolves organization and tenant membership; an unchecked client-supplied tenant ID is not trusted.
- **Immutable session binding:** each authenticated Engine session remains attached to its configured tenant identity and upstream database.
- **Least privilege:** upstream database credentials and native grants are limited to the operations required by the tenant.
- **Database-specific enforcement:** PostgreSQL roles/RLS and MySQL/MariaDB accounts/grants are distinct controls, not interchangeable labels.
- **Protocol and connection controls:** each gateway validates its own protocol and authentication exchanges and applies configured TLS, connection limits and upstream timeouts.
- **Independent verification:** each database adapter has its own real-database tests for allowed and prohibited operations.
- **Management separation:** Cloud connectivity does not replace runtime Engine enforcement or create a SQL bypass.

These controls complement application authorization, secret management, network security, database hardening, backups and deployment review.

## Start with the Engine

The default local quickstart starts the PostgreSQL reference environment:

```bash
git clone https://github.com/Proxima-Engine/proxima-engine.git
cd proxima-engine
docker compose -f compose.dev.yaml up --build -d
```

Check health and status:

```bash
curl -fsS http://127.0.0.1:9090/healthz
curl -fsS http://127.0.0.1:9080/api/status
```

Continue with the [PostgreSQL quickstart](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/QUICKSTART.md). For other database targets, use the dedicated [MySQL](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/MYSQL.md) and [MariaDB](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/MARIADB.md) guides. Do not reuse PostgreSQL role or connection configuration for MySQL or MariaDB.

## Cloud-connected operations

Agata Proxima Cloud provides organization and team management, tenant and policy workflows, Engine node registration, deployment coordination, verification records and audit-oriented operations. Enrolled Engine nodes connect to Cloud through their management identity and channel.

The customer controls the Engine host, upstream database, network reachability, secret storage and deployment execution. SQL traffic remains on the Engine data plane. The hosted Cloud service is a separate proprietary product; the Engine's MIT license applies only to the source covered by the public Engine repository.

Read the [Engine and Cloud integration architecture](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/CLOUD_CONTROL_PLANE.md).

## Documentation

| Guide | Purpose |
|---|---|
| [Quickstart](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/QUICKSTART.md) | Start the local reference environment and send SQL through the Engine |
| [Architecture](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/ARCHITECTURE.md) | Engine components, request lifecycle and trust relationships |
| [Tenant isolation](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/TENANT_ISOLATION.md) | Tenant identity, roles, grants, RLS and negative tests |
| [PostgreSQL](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/POSTGRESQL.md) | PostgreSQL setup, role routing, RLS and pooling |
| [MySQL](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/MYSQL.md) | MySQL gateway setup, identity registry and verification |
| [MariaDB](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/MARIADB.md) | MariaDB setup, compatibility and verification |
| [Configuration](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/CONFIGURATION.md) | Environment variables, defaults and validation |
| [Deployment](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/DEPLOYMENT.md) | Container deployment, TLS, networking and persistence |
| [Security](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/SECURITY.md) | Threat assumptions and runtime security requirements |
| [Verification](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/VERIFICATION.md) | Database-backed tests, acceptance and evidence |
| [Cloud integration](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/CLOUD_CONTROL_PLANE.md) | Management-plane model and Engine connectivity |
| [Full documentation index](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/README.md) | Complete technical documentation |

## Work with the project

- [Inspect the Engine source](https://github.com/Proxima-Engine/proxima-engine)
- [Report a reproducible Engine issue](https://github.com/Proxima-Engine/proxima-engine/issues)
- [Read the contribution guide](https://github.com/Proxima-Engine/proxima-engine/blob/main/CONTRIBUTING.md)
- [Report a security vulnerability](https://github.com/Proxima-Engine/proxima-engine/blob/main/SECURITY.md)
- [Join the Discord community](https://discord.gg/xsYSuDMzg)

[Agata Proxima website](https://agataproxima.com) · [LinkedIn](https://www.linkedin.com/company/agata-proxima/) · [X](https://x.com/Worldtwins25) · [Discord](https://discord.gg/xsYSuDMzg)

---

**Agata Proxima — tenant identity enforced in the database connection path.**
