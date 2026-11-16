# Services Documentation Index

This directory documents system-level userspace daemons and background services for Wicofe.

## Scope
- Daemon responsibilities, lifecycle, configuration, and IPC.
- Logging, device policy, user/session, power, time, and maintenance services.
- Privilege model, restart behavior, health reporting, and service integration.
- Service-specific tests and operational diagnostics.

## Ownership
[Osynit](https://github.com/Waizusora/Osynit) owns PID 1, service definitions, dependency ordering, supervision, and system shutdown. Wicofe-Services owns individual daemon implementations and their service-facing contracts. Kernel mechanisms remain in [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel).

## Security
Each service must document privileges, IPC access controls, secrets, input validation, failure/restart policy, and shutdown behavior. Prefer least privilege.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
