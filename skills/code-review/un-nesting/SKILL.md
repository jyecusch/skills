---
name: un-nesting
description: Flatten deeply nested code. Use in any review or refactor when functions approach 3+ indentation levels, have an arrow/pyramid shape, bury the happy path inside conditionals, or interleave validation with core logic.
---

# Un-nesting

Each nesting level is another condition the reader must hold in working memory at once — and in a diff, the governing conditions usually sit off-screen above the change, so the reviewer can't even see what must be true for the changed line to run. Treat depth beyond 3 as a defect (the Linux kernel style guide's rule).

Apply these moves in order:

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

- **Extraction, after guards**: keep straightforward logic together. Extract only when the remaining function still contains distinct, non-trivial phases or an independently meaningful domain operation or boundary. The parent should become a coherent sequence of happy-path steps (`processIncoming(); processDownloads(); waitForWork();`), and each trustworthy name should make opening the helper optional. If caller-relevant behavior is surprising from the name, rename it or keep the logic inline. Do not extract merely to reduce indentation, line count, or enable isolated testing. Prefer explicit inputs and outputs when extraction is warranted.

Done right, no function requires tracking more than a couple of conditions simultaneously, and digging into any step is optional rather than mandatory.
