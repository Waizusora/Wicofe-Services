# Frenit External Service Integration 0.1

- **Status:** Proposed integration guidance; native service supervision is not verified.
- **Owner:** Wicofe-Service for daemon implementations; Frenit for init/service lifecycle policy.
- **Date:** 2026-11-18
- **Cross-component contract:** [WIC-SPEC-BOOT-FRENIT-001](https://github.com/Waizusora/Wicofe-Spec/blob/main/boot/FRENIT_INTEGRATION_CONTRACT_0.1.md).

## 1. Responsibility split

[Frenit (Free Init)](https://github.com/Waizusora/Frenit) is the canonical Wicofe init-system and service-management project. Frenit owns service definitions, dependency policy, process supervision, lifecycle transitions, restart policy, operating targets and shutdown orchestration.

Wicofe-Service owns individual userspace daemon behavior, configuration, domain IPC, privilege requirements, health reporting and service-specific tests. A daemon must not assume that it is PID 1 or independently replace the system service manager.

## 2. External service domains

Examples include device policy/discovery, logging backends, user and session management, power policy, time synchronization, storage administration and maintenance services. Network, audio, input and graphics services may live in their narrower subsystem repositories.

Frenit supervises service processes; it does not absorb their domain logic into the init core.

## 3. Process and lifecycle boundary

Service daemons should be designed to run under an external supervisor and must not rely on implicit shell execution. Foreground-first operation is a preferred integration direction, but concrete service types, readiness protocol and lifecycle states remain owned by Frenit's implementation and accepted specifications.

Before native supervision is enabled, Wicofe-Kernel and Wicofe-ABI must provide tested process creation/exec, exit status, wait/reap, PID identity, signal delivery, file descriptors and required IPC semantics. Do not assume Linux process ABI or unsupported syscalls.

## 4. Service metadata ownership

Frenit is the sole owner of its service-unit schema and dependency model. Wicofe-Service may document daemon-specific requirements and propose metadata, but must not create a competing unit format or hardcode Frenit-private implementation details.

Any service metadata must eventually identify, as applicable, executable and argument vector, environment policy, working directory, identity/privileges, timeout, restart behavior, dependency edges, readiness requirements and required platform capabilities. The exact schema must be taken from Frenit's canonical specification when published.

## 5. Dependency and failure behavior

Daemon authors must document boot-critical versus best-effort dependencies, expected exit behavior, restart safety, cleanup and failure propagation. Restart loops must be bounded by the supervisor policy. Dependency ordering and failure recovery are implemented by Frenit, not duplicated inside each daemon.

## 6. Security and IPC

Each daemon documents its privilege boundary, IPC endpoints, peer authentication, authorization, secrets handling, input validation and filesystem access. Prefer least privilege and narrowly scoped IPC permissions. Do not assume a manager socket path, transport, wire format or credential structure until Frenit and Wicofe-ABI define it.

## 7. Validation

Cross-repository tests should cover foreground lifecycle, start/stop timeouts, dependency ordering, failure propagation, restart limits, clean shutdown, malformed metadata rejection and privilege/IPC access controls. These remain planned tests; documentation or host-only tests do not prove native Wicofe supervision.

No daemon should claim Frenit-managed native lifecycle until the Frenit PID 1 and kernel/ABI integration gates pass.
