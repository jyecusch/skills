# code-review

Skills for reviewing (and writing) code with a focus on reviewability: could a diff reviewer, seeing only the change, verify it's correct?

`code-critique` is the entry point — it orchestrates a full review and leans on the others. Each of the rest also stands alone for a focused pass.

| Skill | What it covers |
| --- | --- |
| [code-critique](./code-critique/SKILL.md) | Full ruthless review of a diff, PR, or codebase: unnecessary code, accidental complexity, misuse-prone APIs, poor reviewability. |
| [abstraction-and-coupling](./abstraction-and-coupling/SKILL.md) | Whether an abstraction earns its keep — base classes, shared interfaces, DRY refactors, generics. |
| [composition-over-inheritance](./composition-over-inheritance/SKILL.md) | Class hierarchies and reuse via subclassing; when to replace inheritance with composition. |
| [dependency-injection](./dependency-injection/SKILL.md) | How code obtains its dependencies; testability without patching internals. |
| [misuse-resistance](./misuse-resistance/SKILL.md) | APIs and signatures that permit caller mistakes; easy to use correctly, hard to use incorrectly. |
| [naming](./naming/SKILL.md) | Identifier names as contracts — abbreviated, vague, or misleading names. |
| [self-documenting-code](./self-documenting-code/SKILL.md) | Replacing explanatory comments and magic numbers with self-explanatory code. |
| [state-and-pipelines](./state-and-pipelines/SKILL.md) | Mutable state, shared flags, and loops that should be pure transformation pipelines. |
| [testing](./testing/SKILL.md) | High-value behavioral tests, faithful boundaries, and restrained use of test doubles. |
| [type-driven-design](./type-driven-design/SKILL.md) | Types that carry domain proof without redundant implementation annotations. |
| [un-nesting](./un-nesting/SKILL.md) | Flattening deeply nested code with guard clauses and early returns. |
| [writing-comments](./writing-comments/SKILL.md) | Comments and documentation that preserve non-obvious rationale and context. |

Test material for these skills lives in [`verification/`](../../verification/) at the repo root.
