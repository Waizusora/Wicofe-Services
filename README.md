# Wicofe-Services

System-level userspace daemons and background services for Wicofe OS.

## Scope

Potential service families include:
- Device discovery and policy integration.
- System logging and journal transport.
- User, session, and login support.
- Power management and system state coordination.
- Time synchronization and scheduled maintenance.
- Service-facing IPC endpoints.

## Boundaries

[Frenit (Free Init)](https://github.com/Waizusora/Frenit) owns init-system and service lifecycle policy, including service definitions, dependency ordering, supervision and shutdown. Native PID 1 integration remains unverified. Individual daemon implementations belong here or in a narrower subsystem repository. Kernel drivers and device primitives remain in Wicofe-Kernel.

## Design principles

Services should run with the least privilege required, expose documented IPC, handle restart and shutdown predictably, and avoid hidden dependencies on private kernel interfaces.

## Status

Initial repository baseline; service inventory and IPC contracts remain to be specified.

## License

Original project code targets MPL-2.0; dependency licenses must be audited.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.

## Wicofe-wide principles

Wicofe is an independent Unix-like, POSIX-compatible operating system and software ecosystem, designed as an alternative to established and widely adopted software ecosystems and technologies, including GNU. Native components must use Wicofe interfaces and must not require Linux libc or GNU compatibility layers. Prefer permissive licenses; avoid GPL/AGPL, and consider LGPL only when no suitable permissive alternative exists. Reuse or fork upstream software when implementing it from scratch would be disproportionately difficult. See the [canonical project principles](https://github.com/Waizusora/Wicofe-Docs/blob/main/PROJECT_PRINCIPLES.md).
