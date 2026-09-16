---
name: dependency-injection
description: Review how code obtains and configures its dependencies. Use when code constructs its own collaborators, threads configuration through layers, branches on mode flags, or is hard to test without patching internals or hitting real services.
---

# Dependency Injection

Dependency injection makes external capabilities and configuration replaceable at an appropriate composition boundary. Use the project's native mechanism: ordinary arguments for local values and policies, constructors where object ownership calls for them, and an effect system's environment for ambient capabilities. DI is not free: every service, interface, layer, and test double adds indirection and can hide whether the real integration works.

Flag:

- **Mode flags with conditionally-required parameters**:
  ```ts
  // smtpHost/smtpPort required when kind === "email";
  // twilioSid/twilioToken required when kind === "sms"
  function send(kind: string, msg: string, smtpHost?, smtpPort?, twilioSid?, twilioToken?)
  ```
  The branching, intermingled protocols, and comment-enforced contract disappear when each mode has a properly typed implementation whose construction requires exactly its own config. Introduce a shared interface only when callers genuinely operate on interchangeable senders (see `misuse-resistance`).
- **Infrastructure configuration threaded through unrelated layers**: request handlers carrying SMTP hosts and API keys they neither interpret nor own, just to hand them deeper. Resolve the choice where the deciding information lives (usually a composition root or factory near startup/request entry) and inject the ready-made collaborator. Continue passing domain values and local policies explicitly.
- **Hardwired infrastructure inside business logic**: `new S3Client()` buried in a business function mixes configuration, lifecycle, and domain behavior. Move infrastructure construction to a composition boundary and pass the established capability in. Do not respond by inventing a test-only mirror interface; use a direct function/value for a small local policy or the project's existing service mechanism for a shared capability.

In Effect code, `Context.Service` and the `Effect` requirement channel are already the injection boundary. Use `Layer` for reusable dependency graphs and shared service lifecycles, and compose those layers near application, subsystem, or test boundaries. Use `Effect.provideService` for a narrow local override. Keep shared service acquisition and release in its layer; use `Effect.acquireRelease` and `Effect.scoped` for operation-local resources. Do not add parallel constructor injection, forwarding wrappers, or interfaces around an existing service. Use ordinary layer arguments for explicit construction options and typed `Config` or `Context.Reference` only when their semantics fit. Prefer current Effect v4 APIs rather than older `Context.Tag` patterns.

Tests should replace an existing capability only when the real dependency is unsafe, unavailable, slow, or nondeterministic. Prefer lightweight real implementations; otherwise provide the existing service shape with a controlled layer or service value. A test double alone does not justify a new production abstraction.

Don't cargo-cult: inject at genuine capability, ownership, lifecycle, configuration, or substitution boundaries. Call deterministic local code directly everywhere else (see `abstraction-and-coupling`).
