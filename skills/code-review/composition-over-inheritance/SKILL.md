---
name: composition-over-inheritance
description: Review class hierarchies and code reuse via subclassing. Use when code uses inheritance, abstract base classes, or protected members, or when reuse is needed and extending a class is tempting.
---

# Composition over Inheritance

Inheritance couples the child to the parent's *entire* structure — every field, method, and assumption — and conflates two separable capabilities: **code reuse** (I want your methods) and **abstraction** (callers shouldn't know which variant they hold). The moment one subclass doesn't fit the parent's shape, the hierarchy needs restructuring, and restructuring a parent class breaks every consumer at once.

The telltale moment:
```ts
class InMemoryDocument extends FileDocument {
  save() { throw new Error("not supported"); }   // inherited API doesn't apply
  load() { throw new Error("not supported"); }
}
```
A subclass stubbing or throwing on inherited methods is a Liskov substitution violation — the parent bundled "document" with "file-backed", and this child is only one of those.

Prefer the two capabilities separately:

- **Composition for reuse**: instead of inheriting `resize()` from an image base class, hold or receive an `Image` and call `resize(image)`. New capabilities (drawing, filtering) become new classes that *take* an image — no existing class changes.
- **Interfaces for abstraction**: define the minimal contract callers need (`interface Saveable { save(): void }`) and tack it onto whichever classes qualify. Parent classes share everything by default; interfaces share only the contract, so they're cheap to add and cheap to change.

Also flag: protected mutable fields (public variables with extra steps), hierarchies more than ~2 deep, `Base*`/`Abstract*` naming (see `naming`).

If inheritance is genuinely warranted — say, a plugin model too expensive to replace — then design for it: a small, explicit, documented protected override API; everything else private and final/sealed, so parent changes can't silently break children.
