# Services Documentation Index

This directory documents individual system-level userspace daemons and their operational contracts.

## Architecture and design

- [System services architecture 0.1](SYSTEM_SERVICES_ARCHITECTURE_0.1.md) — draft service taxonomy, ownership boundaries, lifecycle, IPC, privilege separation, configuration, recovery and validation.
- [OSynit external service integration 0.1](OSYNIT_SERVICE_INTEGRATION_0.1.md) — OSynit owns lifecycle; this repository owns daemon domain behavior.

## Scope
- Daemon responsibilities, lifecycle, configuration, and IPC.
- Logging, device policy, user/session, power, time, and maintenance services.
- Privilege model, restart behavior, health reporting, and service integration.
- Service-specific tests and operational diagnostics.

## Ownership
[Osynit](https://github.com/Waizusora/Osynit) owns PID 1, service definitions, dependency ordering, supervision, restart policy and system shutdown. Wicofe-Services owns individual daemon implementations and their service-facing contracts. Kernel mechanisms remain in [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel), with public syscall contracts owned by [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI).

## Security
Each service must document privileges, IPC access controls, secrets, input validation, failure/restart policy, and shutdown behavior. Prefer least privilege.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
