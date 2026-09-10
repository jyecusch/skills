---
name: misuse-resistance
description: Review APIs and function signatures for designs that permit developer mistakes. Use when reviewing or designing any public interface, constructor, auth/validation flow, or multi-step protocol — especially where safety depends on callers doing the right thing.
---

# Misuse Resistance

Interfaces must be easy to use correctly and hard to use incorrectly (Scott Meyers; the "pit of success"). A design that relies on developer discipline — remembering to call something, reading a comment, knowing the convention — is a defect, because a competent-but-rushed developer will eventually get it wrong. Hunt for:

- **False affordances**: a signature that implies safety it doesn't provide.
  ```ts
  function createUser(name, email, role) { return { name, email, role }; }
  ```
  Callers reasonably assume a `create` function validates or enforces something; this one is an object literal in a trench coat, and its existence *discourages* callers from validating. Give it a real job (validate, normalize, apply invariants) or delete it and use a literal openly.

- **Temporal coupling**: correctness depends on call order that nothing enforces.
  ```ts
  verifySession(token);                 // step 1 — easy to forget
  const ctx = buildContext(req.body);  // step 2 — also accepts unverified data!
  ```
  Fix by fusing the sequence (`contextFromToken(token)` verifies internally) or by making the later step require unforgeable proof of the earlier one (it accepts only the type that step 1 returns, and nothing else can produce that type).

- **Validation separated from construction**: "parse, don't validate" (Alexis King). Don't check a string is a valid email and keep passing the string — parse it once into an `EmailAddress` type whose only constructor does the check, then require `EmailAddress` downstream. Possession of the value *is* proof of validity; unvalidated data becomes unrepresentable ("make illegal states unrepresentable"). Full treatment — branded domain types, deriving types from constants, correlated fields — in `type-driven-design`.

- **Mode flags and conditionally-required parameters**: `send(kind, ..., smtpHost?, twilioToken?)` where a comment explains which optionals go with which `kind`. The contract lives in prose, so forgetting a value fails at runtime. Split into one type per mode, each constructor requiring exactly its own inputs — forgetting one becomes a compile/startup error.

For every entry point ask: what's the worst plausible mistake a rushed caller could make? If the design permits it, flag it.
