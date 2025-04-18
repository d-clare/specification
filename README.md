# DClare Specification

The DClare Specification defines a vendor-neutral, declarative, and extensible standard for building, coordinating, and executing AI agents and agentic processes.

[![License](https://img.shields.io/github/license/d-clare/specification)](LICENSE)
[![Build Status](https://img.shields.io/github/actions/workflow/status/d-clare/specification/test.yml?branch=main)](https://github.com/d-clare/specification/actions)
[![Release](https://img.shields.io/github/v/release/d-clare/specification?include_prereleases)](https://github.com/d-clare/specification/releases)
[<img src="http://img.shields.io/badge/Website-blue?style=flat&logo=google-chrome&logoColor=white">](https://d-clare.ai/) 
[<img src="https://img.shields.io/badge/LinkedIn-blue?logo=linkedin&logoColor=white">](https://www.linkedin.com/company/d-clare/)

---

## About

DClare introduces a declarative agent coordination model that enables developers to define and orchestrate intelligent agents without coupling logic to infrastructure or runtime details.

The specification is designed to be:

- **Declarative** — Define what your agents do, not how they do it
- **Composable** — Use building blocks like kernels, toolsets, and processes to create scalable systems
- **Portable** — Vendor-neutral and runtime-agnostic
- **Interoperable** — Supports standard communication protocols such as [A2A](https://github.com/google/A2A)
- **Extensible** — Easily define custom components and strategies

---

## Ecosystem

The DClare ecosystem is composed of modular components designed for seamless integration and runtime execution.

### DSL

The DSL defines the core schema used to describe:

- **Agents**: Hosted or remote intelligent actors
- **Kernels**: Reasoning engines backing agents
- **Processes**: Convergence/synthesis logic for multi-agent workflows
- **Memory**: Agent-accessible memory mechanisms
- **Toolsets**: Actionable function collections
- **Authentication**: Credential and access configuration
- **Communication Channels**: Protocol definitions (e.g., A2A)

### SDKs

SDKs are available to help developers work with DClare definitions in various languages:

- [.NET](https://github.com/d-clare/sdk-net)
- [Other SDKs coming soon...]

SDKs can be used to parse, validate, author, and execute specifications programmatically.

### Runtimes

| Name | Description |
|------|-------------|
| [DClare Runtime](https://github.com/d-clare/runtime) | Official modular runtime for executing DClare-defined agents and processes. Built in .NET and designed for extensibility. |

---

## Documentation

The DClare Specification documentation includes:

- [DSL Overview](docs/dsl.md): Core concepts, syntax, and usage
- [DSL Schema](schemas/dsl.schema): JSON Schema used to validate and author declarative manifests, supporting both YAML and JSON formats
- [DSL Component Reference](docs/dsl-reference.md): Formal definition of each component
- [Protocol Support](docs/protocols.md): Communication mechanisms like A2A
- [Examples](examples/README.md): Real-world usage examples
- [Use Cases](docs/use-cases.md): Practical applications of the spec

---

## Community

DClare is an open community-driven project. We welcome contributions, ideas, feedback, and participation from developers, researchers, and users of intelligent agent systems.

### Communication

- Email: [contact@d-clare.ai](mailto:contact@d-clare.ai)
- GitHub Discussions (coming soon)
- Community meetings and roadmap discussions (to be announced)

### Governance

The DClare project follows an open governance model and operates under the principles of transparency, openness, and meritocracy.

For more details, see [GOVERNANCE.md](GOVERNANCE.md)

### Code of Conduct

We are committed to fostering a welcoming and harassment-free environment for everyone.  
Please review our [Code of Conduct](CODE_OF_CONDUCT.md).

---

## Support

DClare is open source and community-driven. If you're interested in supporting its development or using it in production, please reach out via [contact@d-clare.ai](mailto:contact@d-clare.ai) or open a GitHub issue to connect.

Want to contribute? See our [CONTRIBUTING.md](CONTRIBUTING.md) guide.
