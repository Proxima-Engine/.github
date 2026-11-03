# Contributing to Agata Proxima projects

The public engineering repository in this organization is [proxima-engine](https://github.com/Proxima-Engine/proxima-engine). It contains the public Rust data plane, tests, and technical documentation. The hosted Agata Proxima Cloud application is a separate proprietary product; changes to its private source do not belong here.

## Before opening a change

1. Read the [Engine README](https://github.com/Proxima-Engine/proxima-engine/blob/main/README.md), [architecture](https://github.com/Proxima-Engine/proxima-engine/blob/main/docs/ARCHITECTURE.md), and repository [contribution guide](https://github.com/Proxima-Engine/proxima-engine/blob/main/CONTRIBUTING.md).
2. Search existing issues and pull requests for related work.
3. For substantial behavior changes, open an issue first to establish the problem and expected behavior.
4. For vulnerabilities, follow the Engine's private [security policy](https://github.com/Proxima-Engine/proxima-engine/blob/main/SECURITY.md). Do not disclose exploitable details publicly.

## Expectations

- Keep changes focused and explain the problem they solve.
- Add or update tests for behavior changes.
- Update documentation when configuration, behavior, security assumptions, or examples change.
- Explain security and compatibility impact.
- Never include credentials, signing keys, tenant tokens, customer data, or private runtime state.
- Do not claim audits, certifications, performance results, or security guarantees without verifiable evidence.

## Review

Pull requests should describe the motivation, implementation, test evidence, and operational or security implications. Maintainers may request changes or additional tests before merging.

The repository-specific contribution guide defines the authoritative local setup and test commands.
