---
name: un-nesting
description: Flatten deeply nested code. Use in any review or refactor when functions approach 3+ indentation levels, have an arrow/pyramid shape, bury the happy path inside conditionals, or interleave validation with core logic.
---

# Un-nesting

Each nesting level is another condition the reader must hold in working memory at once — and in a diff, the governing conditions usually sit off-screen above the change, so the reviewer can't even see what must be true for the changed line to run. Treat depth beyond 3 as a defect (the Linux kernel style guide's rule).

Two moves:

- **Inversion**: flip conditions into guard clauses with early returns.
  ```ts
  // before: happy path buried          // after: guards, then happy path
  if (user != null) {                   if (user == null) return;
    if (user.isActive) {                if (!user.isActive) return;
      grantAccess(user);                grantAccess(user);
    }
  }
  ```
  Error and edge cases exit immediately; validation collects at the top as a declared precondition gate; the happy path runs straight down the left margin. Once a guard has returned, the reader *permanently discards* that condition instead of carrying it to the closing brace.

- **Extraction**: pull inner blocks — loop bodies, switch arms, distinct phases — into their own well-named functions, so the parent reads as a flat sequence of steps (`processIncoming(); processDownloads(); waitForWork();`). Prefer extracting *pure* functions (explicit inputs and outputs, no shared mutable state): they can be verified and tested in isolation, without knowing the caller's context.

Done right, no function requires tracking more than a couple of conditions simultaneously, and digging into any step is optional rather than mandatory.
