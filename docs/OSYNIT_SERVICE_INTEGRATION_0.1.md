# OSynit External Service Integration 0.1

- **Status:** Proposed integration contract
- **Owner:** Wicofe-Services for daemon implementations; OSynit for lifecycle
- **Date:** 2026-11-16

## 1. Responsibility split

OSynit (Open System Initialize) owns system initialization, service definitions, dependency resolution, process supervision, service lifecycle, system state, failure recovery, and shutdown ordering.

Wicofe-Services owns individual userspace daemon behavior, configuration, domain IPC, privilege requirements, health reporting, and service-specific tests. A daemon must not assume that it is PID 1 or independently replace the system service manager.

## 2. External service domains

Examples include device policy/discovery, logging backends, user and session management, power policy, time synchronization, storage administration, and maintenance services. Network, audio, input, and graphics services may live in their narrower subsystem repositories.

OSynit supervises these processes; it does not embed their domain logic into its mandatory core.

## 3. Service process contract

Initial OSynit service support is foreground-first:

- `oneshot`: execute to completion and report success/failure.
- `simple`: supervise a foreground main process.

Daemons should remain in the foreground and avoid self-daemonization unless a separately reviewed service type is introduced. They must handle configured termination signals, close resources, report meaningful exit status, and avoid relying on an implicit shell.

## 4. Unit metadata

A service unit should declare, as applicable:
- stable service name and description;
- executable path and argument vector;
- working directory and explicit environment policy;
- user/group or capability requirements once the identity model is specified;
- start/stop timeout and termination signal;
- restart policy and rate limits;
- dependency and ordering edges;
- optional readiness/health integration;
- required platform capabilities.

Unit schema is owned by OSynit. Wicofe-Services may propose service-specific metadata but must not fork the canonical unit format.

## 5. Dependency and failure behavior

The initial dependency relationship set is `Requires`, `Wants`, `Before`, `After`, `Conflicts`, and `OnFailure`. Service authors must document whether dependencies are boot-critical or best-effort.

Restart behavior must be bounded and rate-limited. A daemon must not create its own unbounded restart loop in conflict with OSynit's supervisor policy.

## 6. Security and IPC

Each daemon documents its privilege boundary, IPC endpoints, peer authentication, authorization, secrets handling, input validation, and filesystem access. Prefer least privilege and narrowly scoped IPC permissions. OSynit's manager socket is not a general-purpose daemon bus.

## 7. Validation

Integration tests must verify foreground lifecycle, start/stop timeout behavior, dependency ordering, failure propagation, restart limits, clean shutdown, malformed unit rejection, and privilege/IPC access controls. A service unit being documented does not imply the daemon is implemented or packaged.
