# VerdinCore documentation

This documentation describes the current VerdinCore platform and its direction. It is written for developers, technical decision-makers, content teams, and anyone evaluating the system.

## Start here

| Document | What it covers |
| --- | --- |
| [Platform overview](overview.md) | Purpose, principles, audiences, and the public/private repository boundary |
| [Features](features.md) | Publishing, administration, media, discovery, analytics, and automation capabilities |
| [Architecture](architecture.md) | System boundaries, application layers, request flow, multi-brand design, and technology choices |
| [Content model](content-model.md) | Pages, posts, taxonomy, blocks, media, and their relationships |
| [Security and operations](security-and-operations.md) | Authentication, input handling, caching, tracking, recurring jobs, and operational behavior |
| [Roadmap](roadmap.md) | Current priorities and longer-term product direction |

## Repository scope

This public repository contains product and architecture documentation plus assets used to present VerdinCore. The CMS implementation is developed in a private repository.

The documentation intentionally explains system behavior without publishing credentials, environment values, deployment-specific paths, private infrastructure details, or proprietary source code.

## Documentation conventions

- **Current** means the capability exists in the private core.
- **Configurable** means a deployment can enable, disable, or tune the capability.
- **Planned** means the capability belongs to the roadmap and should not be treated as available.
- Technology versions describe the current development baseline and can change as the platform evolves.

## Suggested reading paths

**Evaluating the platform:** [Overview](overview.md), [Features](features.md), then [Roadmap](roadmap.md).

**Understanding the implementation:** [Architecture](architecture.md), [Content model](content-model.md), then [Security and operations](security-and-operations.md).

**Contributing to these docs:** Start with the root [README](../README.md), keep statements aligned with the current private core, and clearly label future work.

