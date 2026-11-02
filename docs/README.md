# VerdinCore documentation

This documentation describes VerdinCore `4.0.1`. It is organized for evaluators, editors, operators, and developers who need different levels of detail.

The application is proprietary and its source is maintained in a private repository. Installation and development guides assume authorized access to that source.

## Choose a reading path

### Evaluate the platform

1. [Platform overview](product/overview.md)
2. [Feature map](product/features.md)
3. [Roadmap](product/roadmap.md)

### Learn the editorial system

1. [Editorial workflow](guides/editorial-workflow.md)
2. [Reusable blocks](guides/reusable-blocks.md)
3. [Media](guides/media.md)
4. [Administration and roles](guides/administration-and-roles.md)

### Run a deployment

1. [Requirements and installation](operations/installation.md)
2. [Configuration](operations/configuration.md)
3. [Deployment](operations/deployment.md)
4. [Scheduled jobs](operations/scheduled-jobs.md)
5. [Security and privacy](operations/security-and-privacy.md)
6. [Backups and recovery](operations/backups-and-recovery.md)

### Understand or extend the codebase

1. [System architecture](architecture/system.md)
2. [Content model](architecture/content-model.md)
3. [Database reference](reference/database.md)
4. [Route reference](reference/routes.md)
5. [Codebase guide](development/codebase.md)
6. [Front-end build](development/frontend.md)
7. [Testing](development/testing.md)

## Documentation map

| Folder | Contents |
| --- | --- |
| [`product/`](product/overview.md) | Product purpose, current features, and roadmap |
| [`architecture/`](architecture/system.md) | Application layers, request flow, storage, and content relationships |
| [`guides/`](guides/editorial-workflow.md) | Day-to-day publishing, blocks, media, brands, themes, and administration |
| [`operations/`](operations/installation.md) | Installation, settings, deployment, maintenance, security, and recovery |
| [`development/`](development/codebase.md) | Project layout, service boundaries, asset builds, and tests |
| [`reference/`](reference/routes.md) | Routes and database tables |

## Scope and conventions

- **Current** means the behavior exists in application version `4.0.1`.
- **Configurable** means an operator can enable, disable, or tune it for a deployment.
- **Planned** means it is a direction, not an available feature or delivery commitment.
- Example values are placeholders. Production credentials and deployment-specific paths are intentionally omitted.

The private application remains the source of truth. Documentation changes should be checked against routes, configuration classes, models, services, schema, and tests before publication.
