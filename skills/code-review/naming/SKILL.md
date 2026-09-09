---
name: naming
description: Review or fix identifier names — variables, functions, classes, modules. Use during any code review or refactor, and whenever names are abbreviated, vague (utils, helper, data, tmp, Base*), or might not match what the code actually does.
---

# Naming

A name is a contract. Readers — above all diff reviewers, who see the call site but not the implementation — act on the name alone. A misleading name causes misuse; a vague name adds cognitive load (principle of least astonishment: code should do what its name makes you expect).

Flag, in order of severity:

- **Misleading names** (worst offense): the reader's natural assumption would be wrong.
  ```ts
  function sanitizeInput(s: string) { return s.trim(); }
  ```
  Every caller now believes injection attacks are handled. Likewise a `get...` that mutates, or a `create...` implying checks it doesn't do. Rename to what it really does (`trimWhitespace`) — or better, make the code honor the name's claim.
- **Abbreviations and single letters**: `usrCnt`, `procDt(d)`. The reader must reverse-engineer your compression scheme. Write `userCount`, `processDate(date)` — autocomplete pays the typing cost, not you. (Idiomatic loop indices like `i` in a 3-line loop are fine.)
- **Types encoded in names**: Hungarian notation (`strName`), `I`-prefixed interfaces (unless the ecosystem convention, e.g. C#), and `Base`/`Abstract` in class names. Callers shouldn't care whether they hold an interface or a class. If the parent seems to need "Base" (`BaseTruck`), the *child* is under-specified: name the parent `Truck` and the child `TrailerTruck`.
- **Missing units**: `delay` — of what? `delaySeconds` at minimum; better, a unit-carrying type (`Duration`, `TimeSpan`) so the compiler makes the unit unmissable.
- **utils/helpers/misc dumping grounds**: a naming struggle usually means the code is in the wrong place. Move functions onto the types they operate on, or into cohesive modules that *can* be named (paging logic → `Paginator`, cookie parsing → `Cookie`). Standard libraries have no `utils` module for a reason.

Test every name: would a rushed reviewer, seeing only this name in a diff, make correct assumptions about its behavior, side effects, and ownership?
