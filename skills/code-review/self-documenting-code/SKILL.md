---
name: self-documenting-code
description: Eliminate comments by making code self-explanatory. Use when reviewing code containing explanatory comments, magic numbers, or complex conditionals — and whenever tempted to write a comment describing what code does.
---

# Self-Documenting Code

A comment explaining *what* code does is a failed refactor. Comments rot — no compiler, test, or linter checks them, so they drift from the code and eventually lie; code cannot lie. Replace the comment with the construct it was compensating for:

- **Named constants** for magic values:
  ```ts
  if (status == 5) { ... }        // 5 means the message was sent
  if (status == MESSAGE_SENT) { ... }   // comment now redundant — delete it
  ```
- **Named intermediates and extracted predicates**, so the condition reads as the comment would have:
  ```ts
  const userIsAuthor = message.authorId === user.id;
  const editWindowOpen = message.ageMinutes() < EDIT_WINDOW_MINUTES;
  if (userIsAuthor && editWindowOpen) { ... }
  ```
  **Local variable first.** A named local is the default: its definition sits directly above its use (visible in the same diff hunk), it adds no API surface, and the name alone documents it — a well-named local should need no comment at all. Escalate to a named function (`canEditMessage(user, message)`) only when the logic spans several concepts, genuinely recurs, or needs isolated testing. Extracting a single boolean comparison into a one-line function is a net loss: it creates a distant definition, a new surface to misuse, and a doc-comment obligation, all to restate what the local's name already said.
- **Types that encode the contract**: `unique_ptr` instead of "// caller must free"; `Optional<Timestamp>` instead of "// -1 means not set"; `Duration` instead of "// in seconds". The compiler enforces types; nobody enforces comments.

Legitimate comments say *why*, which code cannot express: performance-motivated weirdness ("unrolled — profiler showed this loop is 40% of frame time"), links to the algorithm, paper, spec, or ticket the code implements, warnings about non-obvious hazards.

Passing this skill's tests is necessary, not sufficient: a comment can restate nothing and carry genuine rationale yet still be unintelligible, because its nouns ("the caller", "the hold") refer to things only the author's conversation named, or it narrates how *other files* use this code. Every comment that survives here must also pass the stranger test in `writing-comments` — flag undecodable rationale as a defect exactly like redundancy.

API documentation is a different thing from comments: docstrings on public interfaces describing the contract — expected states, error conditions, thread safety — are for *users* who shouldn't have to read the implementation. Keep those, adjacent to the code so they stay in sync (Doxygen/pydoc/JavaDoc style).

Flag every remaining comment that merely restates its code.
