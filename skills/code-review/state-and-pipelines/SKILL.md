---
name: state-and-pipelines
description: Review mutable state and data-transformation code. Use when reviewing loops that filter, transform, accumulate, or sort; shared mutable variables or flags; or functions mixing computation with side effects.
---

# State and Pipelines

State is where bugs live: once a function's result depends on things other than its inputs, the reader must reconstruct history to reason about it. A **pure function** — output fully determined by inputs, no side effects — can be verified from its own lines alone, which is exactly what a diff reviewer can actually check.

Flag:

- **Manual bookkeeping loops** doing filter/transform/accumulate:
  ```ts
  const names: string[] = [];
  for (let i = 0; i < users.length; i++) {
    if (users[i].isActive) names.push(users[i].email);
  }
  // ↓ states what, hides how — nothing to get wrong
  const names = users.filter(u => u.isActive).map(u => u.email);
  ```
  Chains of `filter → map → sort → slice` form a declarative pipeline; each stage is independently readable and there's no index or accumulator to fumble.
- **Computation interleaved with effects**: a function that calculates a total *and* writes the database *and* sends an email makes domain reasoning depend on infrastructure history. Separate a coherent pure calculation (`computeInvoice(items): Invoice`) when that creates a real domain operation, then orchestrate I/O through the project's native effect mechanism. Do not fragment straightforward workflows or introduce interfaces merely to unit-test each call; test the shell at the smallest boundary that faithfully exercises its behavior.
- **Mutable flags steering distant control flow**: a module-level `connectionDisabled = true` set in one function and consulted in another is action at a distance — no local reading of either function reveals the link. Return values or pass state explicitly so the data flow is visible in signatures.

Don't demand purity everywhere: I/O and state are the program's entire job (computers are state machines). The goal is a thin, obvious stateful shell around a pure, verifiable core — not a monastery.
