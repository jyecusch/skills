---
name: dependency-injection
description: Review how code obtains and configures its dependencies. Use when code constructs its own collaborators, threads configuration through layers, branches on mode flags, or is hard to test without patching internals or hitting real services.
---

# Dependency Injection

Dependency injection is just this: code that needs a collaborator receives it (usually at construction) instead of constructing or importing it at the point of use. The receiving code depends on a minimal interface and doesn't know which implementation it holds. That one move buys configurability (swap implementations in one place) and testability (swap in fakes) for free.

Flag:

- **Mode flags with conditionally-required parameters**:
  ```ts
  // smtpHost/smtpPort required when kind === "email";
  // twilioSid/twilioToken required when kind === "sms"
  function send(kind: string, msg: string, smtpHost?, smtpPort?, twilioSid?, twilioToken?)
  ```
  The branching, the intermingled protocols, and the comment-enforced contract all disappear if each mode is its own implementation of one interface (`Sender.send(msg)`), whose constructor *requires* exactly its own config — forgetting a value becomes an immediate error, not a latent runtime bug (see `misuse-resistance`).
- **Configuration threaded through layers**: request handlers carrying SMTP hosts and API keys they never use, just to hand them to something deeper. Resolve the choice where the deciding information lives (usually a composition root or factory near startup/request entry) and inject the ready-made collaborator; intermediate code should carry nothing.
- **Hardwired construction inside logic**: `new S3Client()` buried in a business function means every test of that function talks to S3. Untestability is the smoke alarm — if you're asking "how do I test a private method?" or reaching for monkey-patching, a seam is missing: extract that piece behind an interface and inject it.

Don't cargo-cult: an interface with one implementation, never swapped in production *or* in tests, is coupling for nothing (see `abstraction-and-coupling`). Inject where there's a genuine seam — variation, isolation, or configuration — and call things directly everywhere else.
