# Pull Request Review Checklist

A repeatable reference for reviewing pull requests. Work through the sections
in order; skip a section only if it's clearly not applicable (e.g., no UI
changes). Treat unchecked "must" items as blockers.

## 1. Understand the change first

- [ ] Read the PR description / linked issue — do you understand *why*, not
      just *what*?
- [ ] Does the diff match the stated intent? No unrelated/opportunistic
      changes bundled in?
- [ ] Is the change scoped appropriately (not too large to review safely)?
- [ ] Are commits/PR title following the repo's conventions?

## 2. Correctness

- [ ] Does the code do what it claims to do? Trace through the logic
      manually for the main path.
- [ ] Are all branches (if/else, switch cases) handled and correct?
- [ ] Are return values, exceptions, and error codes checked and handled?
- [ ] Off-by-one errors in loops, slices, ranges, pagination?
- [ ] Correct handling of `null`/`nil`/`undefined`/`None`?
- [ ] Type coercion or implicit conversion bugs?
- [ ] Are external API/library calls used correctly (right params, correct
      assumptions about behavior)?
- [ ] Are there race conditions or ordering assumptions in concurrent/async
      code?
- [ ] Does the change interact correctly with existing state/config/feature
      flags?
- [ ] Are database migrations backward compatible and reversible?
- [ ] Is idempotency required and, if so, guaranteed (retries, webhooks,
      queue consumers)?

## 3. Security

- [ ] Is all external/user input validated and sanitized (including
      headers, query params, file uploads)?
- [ ] Any injection risk (SQL, command, template, HTML/XSS, LDAP, log
      injection)?
- [ ] Are secrets, tokens, or credentials hard-coded, logged, or exposed in
      error messages?
- [ ] Are authentication and authorization checks present on every new
      endpoint/action (not just the happy-path UI)?
- [ ] Is access control enforced server-side, not just client-side?
- [ ] Are permissions checked at the right granularity (object-level, not
      just route-level) to prevent IDOR?
- [ ] Is sensitive data encrypted in transit and at rest where required?
- [ ] Are dependencies new/updated? Any known CVEs? Pinned versions?
- [ ] Is user-supplied data escaped before rendering (templates, logs,
      shell commands)?
- [ ] Are rate limits / abuse protections needed for new public endpoints?
- [ ] Does error handling avoid leaking stack traces, internal paths, or
      system details to end users?
- [ ] Are file paths, filenames from user input validated against path
      traversal?
- [ ] Are CORS, CSRF, and cookie settings (`Secure`, `HttpOnly`,
      `SameSite`) correct for new endpoints?

## 4. Performance

- [ ] Any obvious N+1 queries or unnecessary round trips (DB, network,
      disk)?
- [ ] Are appropriate indexes in place for new/changed queries?
- [ ] Is data loaded lazily/paginated where volumes could be large?
- [ ] Any unnecessary allocations, copies, or synchronous blocking calls in
      hot paths?
- [ ] Are caches used/invalidated correctly (no stale data, no cache
      stampede)?
- [ ] Does the change add any unbounded loops, recursion, or memory growth
      (e.g., unbounded queues, large in-memory buffers)?
- [ ] Are timeouts and retries configured with backoff, to avoid cascading
      failures?
- [ ] Are large payloads streamed instead of buffered in memory?
- [ ] If this touches a critical path, has it been profiled or
      load-tested, or does it need to be?

## 5. Readability & maintainability

- [ ] Are names (variables, functions, classes) clear and consistent with
      the codebase's conventions?
- [ ] Is the code as simple as it can be for the problem (no
      over-engineering, no premature abstraction)?
- [ ] Are complex or non-obvious sections commented with *why*, not *what*?
- [ ] Is duplicated logic factored out appropriately, without introducing
      forced/wrong abstractions?
- [ ] Are magic numbers/strings replaced with named constants?
- [ ] Does the change fit existing architectural patterns and boundaries
      (layering, module ownership)?
- [ ] Is dead code, commented-out code, or debug logging left behind?
- [ ] Are TODOs/FIXMEs tracked (linked to an issue) rather than left as
      loose comments?
- [ ] Does the diff avoid unrelated formatting/whitespace churn that
      obscures the real change?

## 6. Test coverage

- [ ] Are new tests added for new behavior?
- [ ] Do tests cover the happy path *and* meaningful failure paths?
- [ ] Are edge cases from section 7 actually exercised by tests, not just
      described?
- [ ] Do tests assert real outcomes (not just "no exception thrown" /
      overly loose assertions)?
- [ ] Are tests deterministic (no reliance on real time, network, random
      values, or ordering)?
- [ ] Is test intent clear from names/structure (arrange-act-assert or
      equivalent)?
- [ ] Were existing tests updated when behavior intentionally changed
      (not just deleted to make CI pass)?
- [ ] Do all tests actually pass locally/in CI, and does coverage tooling
      (if any) show no meaningful regression?
- [ ] For bug fixes: is there a regression test that would have caught the
      original bug?

## 7. Edge cases

- [ ] Empty / null / zero-length inputs (empty string, empty array, empty
      file).
- [ ] Boundary values (min/max, zero, negative numbers, very large
      numbers).
- [ ] Duplicate or out-of-order events/requests.
- [ ] Concurrent requests modifying the same resource.
- [ ] Partial failures (e.g., one of N downstream calls fails midway).
- [ ] Network failures, timeouts, and retries on external calls.
- [ ] Unicode, encoding, and locale differences (dates, currency, RTL
      text).
- [ ] Clock/timezone edge cases (DST transitions, leap seconds/years,
      UTC vs. local time).
- [ ] Backward compatibility with old clients/data formats during rollout.
- [ ] Feature flag on/off and partial-rollout states.
- [ ] Permission edge cases (revoked access mid-session, deleted
      dependent resource).

## 8. Operational readiness

- [ ] Are logs/metrics/traces added or updated for new failure modes?
- [ ] Are alerts/dashboards updated if this changes critical behavior?
- [ ] Is there a safe rollback path (feature flag, reversible migration,
      no destructive one-way changes)?
- [ ] Are config/env var changes documented and defaulted safely?
- [ ] Is user-facing documentation (README, API docs, changelog) updated?
- [ ] Does the PR include enough context for on-call/future readers to
      understand the change without re-reading the whole diff?

## 9. Final pass

- [ ] Would you be comfortable being paged for this change at 3am?
- [ ] Is there anything you'd want to leave as a comment for the author
      to think about, even if not blocking?
- [ ] Blocking issues are called out explicitly and separated from
      suggestions/nits.
