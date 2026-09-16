---
name: abstraction-and-coupling
description: Judge whether an abstraction earns its keep. Use when reviewing shared base classes, common interfaces, DRY refactors, generics, or any layer introduced to remove duplication — and before creating one.
---

# Abstraction and Coupling

Every abstraction adds coupling: everything sharing it is now constrained to change together, and can only diverge by breaking the abstraction. Duplication is a visible, local cost you pay once; coupling is invisible at design time and charges interest at every future change. So: prefer a little duplication over the wrong abstraction (Sandi Metz).

Worked example: `EmailNotifier` and `SmsNotifier` both store a `recipient` string, so someone extracts `BaseNotifier` holding that field. Value gained: one assignment statement deduplicated. Cost: both classes are now constrained to a single-string-recipient model — a future `WebhookNotifier(url, secret)` or multi-recipient notifier breaks the abstraction. Verdict: not worth it; delete the base class.

Flag abstractions whose coupling outweighs their value:

- Shared parents/helpers extracted only to save trivial code — a couple of assignments, one duplicated call. Deduplicating *logic* can be worth coupling; deduplicating *syntax* almost never is. A short helper is different when it names an independently meaningful domain operation or boundary and its trustworthy name makes navigation unnecessary.
- Interfaces or base classes with a single implementation and no capability, ownership, lifecycle, configuration, or production variation boundary. A test double alone does not justify a new abstraction; tests should normally implement an existing boundary.
- DRYing *coincidental* duplication: code that looks alike today but answers to different owners — e.g. `adminDiscount` and `loyaltyDiscount` both happen to be `price * 0.9` this quarter. Merge them and the first divergent requirement forces an awkward un-merge.

An abstraction can earn its keep when it names an independently meaningful domain operation, protects an invariant or complex boundary, **separates deciding from doing** (factories, polymorphic call sites, a scheduler that retries "a task" without knowing which), or unifies genuinely interchangeable variants. A stable infrastructure capability can earn a boundary with one live implementation when it isolates configuration or resource lifetime. In Effect code, a `Context.Service` shape and alternate layers already provide that boundary; do not add a parallel interface or wrapper.

Verdict per abstraction: name the coupling it introduces, name the value it delivers, and recommend inlining it if the ledger is negative.
