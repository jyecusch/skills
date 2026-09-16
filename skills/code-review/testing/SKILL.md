---
name: testing
description: Decide whether and how to test a feature, bug fix, regression, or integration. Use when adding or reviewing tests, choosing test boundaries or doubles, or considering TDD. Prioritize meaningful behavioral confidence over test count, coverage, or ritual.
---

# High-Value Testing

Add a test only when it has an independent oracle and catches a plausible observable regression not better prevented by an existing test, type, schema, compiler check, or static rule. If stronger, cheaper enforcement can faithfully express the invariant, use it instead. A valuable test fails for the defect, survives a behavior-preserving refactor, and exercises the smallest faithful boundary.

Test public contracts as users observe them: returned values, visible state, persisted data, emitted messages, or externally meaningful effects. Do not assert private helpers, incidental call order, component structure, or internal state. Derive expected values from the specification, a reproduced defect, or a worked example — never the implementation's algorithm.

Choose by risk, not a fixed pyramid or trophy: focused tests for pure domain logic and edge cases; integration tests for databases, files, serialization, framework wiring, queues, and service boundaries; contract tests between independently evolving implementations; end-to-end tests only for critical risks narrower tests cannot cover.

Prefer fast, deterministic, safe real collaborators. Otherwise use an existing maintained fake or narrow stub at an actual external boundary. Use mocks and spies only when the interaction is the contract; never add a production interface, helper, or DI seam solely to make calls mockable. Every replaced collaborator removes confidence in the real integration.

For a defect, prove the test fails before the fix and passes afterward when practical. Test-first can clarify new behavior, but do not force a low-value test or premature seam. When TDD is requested, repeat: write one behavioral test, verify it fails for the expected reason, implement the minimum to pass, then refactor while green.

Skip or delete tests that restate types, framework behavior, constants, doubles, or implementation structure; duplicate stronger coverage; derive expectations from the implementation; or cost more than the risk they reduce.

When changing code, audit its existing tests against this standard. Delete or consolidate nearby tests that fail it; do not preserve or imitate low-value tests for consistency. Before removal, identify what meaningful failure each test uniquely detects, and retain or replace it if that protection is not provided elsewhere.

Use the project's native test facilities and existing capability boundaries instead of creating parallel abstractions. Prefer virtual time for clock semantics, scoped fixtures for resource cleanup, and practical live implementations for filesystem, process, and locking behavior. Use live time only when the contract depends on the real scheduler or platform clock.

Treat flakiness as a defect and coverage only as a way to locate untested code. Optimize for confidence per unit of execution and maintenance cost.
