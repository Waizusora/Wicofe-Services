# System Services Architecture 0.1

**Status:** Draft proposal. This document defines ownership, service boundaries and operational requirements. It does not claim that the listed daemons or IPC protocols are implemented.

## 1. Purpose

Define how system-level userspace services are organized, isolated, configured and integrated in Wicofe OS. Services should remain independently testable and restartable, with explicit contracts to Osynit, the kernel ABI and desktop components.

## 2. Ownership boundaries

| Component | Responsibility |
|---|---|
| Wicofe-Services | Implement individual system daemons, their configuration, IPC endpoints, diagnostics and service-specific tests |
| Osynit | PID 1, early userspace, service definitions, dependency graph/order, supervision, restart policy, readiness/health integration and shutdown |
| Wicofe-Kernel | Process, memory, file descriptor, IPC and security primitives |
| Wicofe-ABI | Canonical public syscall identifiers, layouts, errors and compatibility policy |
| Wicofe-Lib | Reusable safe Rust userspace wrappers and common client libraries |
| Wicofe-Specs | Cross-repository system contracts and architecture decisions |
| WDE | Desktop/session-facing controls and presentation; not the owner of system daemon internals |

Osynit and Wicofe-Services are complementary: Osynit manages service processes; Wicofe-Services supplies the service programs. A daemon must not implement a second service manager or privately depend on Osynit internals.

## 3. Service taxonomy

The initial service inventory is a proposal, not a release commitment. Candidate domains include:

- Device discovery and policy coordination.
- System log collection/forwarding and journal transport.
- User, login and session coordination.
- Power and system-state coordination.
- Time synchronization.
- Network policy/control-plane services, coordinated with Wicofe-Network.
- Audio policy and device integration, coordinated with Wicofe-Audio.
- Storage discovery/mount orchestration, coordinated with Wicofe-Storage.

A service should exist only when it has a distinct responsibility, lifecycle, privilege boundary or IPC contract. Avoid splitting every helper into a daemon; excessive process boundaries increase startup, IPC and recovery complexity.

## 4. Process and lifecycle contract

Each daemon must document:
- executable path and required runtime/environment;
- service identity and privilege/capability requirements;
- startup prerequisites and declared dependencies;
- foreground/background behavior (prefer foreground supervision);
- readiness semantics and startup timeout;
- shutdown signal/request and graceful-stop timeout;
- restart safety, restart limits and backoff expectations;
- state persistence and recovery after unexpected termination;
- logs, health indicators and diagnostic commands.

Osynit owns restart decisions and service ordering. A daemon reports readiness and health through the agreed manager contract; it must not independently daemonize or fork away from supervision unless the service format explicitly supports that behavior.

Startup success must not be inferred solely from process creation. Readiness should mean that the service can fulfill its documented contract. Avoid cyclic dependencies; dependency graphs must be validated before activation.

## 5. IPC and API design

Every service protocol must specify:
- transport and endpoint naming;
- protocol version and capability negotiation;
- caller identity and authorization;
- message framing, maximum sizes and encoding;
- request IDs, cancellation and timeout behavior;
- synchronous versus asynchronous operations;
- event ordering, subscription and backpressure;
- error codes and partial-completion semantics;
- behavior when the service restarts or a connection becomes stale.

Use the canonical kernel ABI for underlying primitives. A service-specific protocol is not automatically a system-wide ABI; promote it to Wicofe-Specs only when multiple repositories or independently developed clients need a shared contract.

Validate all client input, bound memory and queue usage, and treat messages as untrusted. Avoid blocking a central event loop on slow clients. Define whether events can be dropped, coalesced or require durable delivery.

## 6. Privilege separation and security

- Run each service with the minimum identity and capabilities required.
- Separate privileged device/configuration operations from unprivileged policy or presentation logic where practical.
- Authenticate IPC peers and authorize each operation, not merely the initial connection.
- Restrict filesystem paths, device nodes, environment variables and inherited file descriptors.
- Avoid storing secrets in command-line arguments or world-readable configuration.
- Define ownership and permissions for sockets, state files, logs and runtime directories.
- Validate configuration before applying it; reject unknown security-sensitive options.
- Document threat boundaries and consequences of service compromise.

A service must not rely on obscurity of its IPC endpoint as access control.

## 7. Configuration and state

Configuration format, defaults, precedence and validation must be documented per service. Prefer declarative, human-readable configuration with explicit schema/version. Distinguish administrator configuration, runtime state and generated cache.

Updates should be applied atomically where possible. A malformed update must not destroy the last known-good configuration. State files need ownership, permissions, corruption handling and migration rules. Do not persist transient device identifiers as if they were stable identities.

## 8. Logging, health and diagnostics

Use structured records with severity, timestamp/clock semantics, service identity and event context. Avoid logging credentials, tokens, private user data or raw sensitive payloads.

Define health states such as starting, ready, degraded and stopping only where their semantics are actionable. A health check must be bounded and must not itself cause service failure. Osynit consumes lifecycle/readiness information; the logging service or transport must not become a hard prerequisite for every daemon to start.

## 9. Failure and recovery

Document behavior for dependency loss, IPC disconnect, device removal, malformed configuration, resource exhaustion, service crash, repeated restart and shutdown timeout. Use bounded retries and backoff. Avoid restart loops that amplify a system-wide failure.

Services should reconnect to restartable peers and revalidate capabilities after reconnection. Persistent operations need idempotency or transaction semantics where duplicate requests could cause harm. A service must fail closed for authorization and destructive operations.

## 10. Testing strategy

Required test layers:
- unit tests for parsing, policy, state machines and authorization;
- protocol tests for version mismatch, malformed messages, oversized payloads, cancellation and backpressure;
- integration tests with a mock Osynit manager contract;
- lifecycle tests for startup ordering, readiness timeout, crash/restart and graceful shutdown;
- privilege tests for unauthorized callers and restricted filesystem/device access;
- fault-injection tests for dependency loss, resource exhaustion and corrupted state;
- target tests on x86-64 and AArch64 when supported.

Tests must distinguish a compiled daemon, a runnable daemon, a supervised daemon and a service validated in a complete booted system.

## 11. Open decisions

- Initial mandatory service inventory and which services are optional.
- Canonical Osynit readiness/health and service-control protocol.
- IPC transport and system-wide endpoint naming policy.
- Identity/capability model and service account provisioning.
- Configuration schema/versioning and runtime directory layout.
- Logging record schema and durable journal ownership.
- Service sandboxing and resource-limit mechanisms.
- Which protocols belong in Wicofe-Specs versus service-local documentation.

## 12. Completion criteria

Before implementation is considered integrated, each service needs an owner, documented purpose, dependency list, privilege model, IPC contract, configuration schema, readiness/shutdown behavior, recovery policy and automated tests. Osynit integration must be reviewed separately; this repository does not define or replace Osynit's PID 1 behavior.
