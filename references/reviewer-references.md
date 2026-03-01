# /reviewer — Reference Library

Sources used to build the expert code reviewer agent. Add new references here as the checklist evolves.

---

## Rust Official Resources

| Resource | What it covers | URL |
|----------|---------------|-----|
| **Rust Style Guide** | Official formatting and naming conventions maintained by the lang team | https://doc.rust-lang.org/nightly/style-guide/ |
| **Rust API Guidelines** | How to design idiomatic, ergonomic Rust APIs — naming, type design, interoperability | https://rust-lang.github.io/api-guidelines/ |
| **Clippy Lints Reference** | Full index of all Clippy lints with explanations — the machine-enforced style guide | https://rust-lang.github.io/rust-clippy/master/ |
| **The Rust Reference** | Language specification — ownership, lifetimes, unsafety, edition differences | https://doc.rust-lang.org/reference/ |
| **Rust Nomicon** | The dark arts of unsafe Rust — required reading before approving any `unsafe` block | https://doc.rust-lang.org/nomicon/ |
| **Comprehensive Rust (Google)** | Google's internal Rust course — async cancellation chapter is essential | https://google.github.io/comprehensive-rust/ |

---

## Code Review Culture & Process

| Resource | Key Insight |
|----------|-------------|
| [Google eng-practices: What to look for](https://google.github.io/eng-practices/review/reviewer/looking-for.html) | The eight review dimensions. Core question: does this improve codebase health? Design > Functionality > Complexity > Tests > Naming > Comments > Style > Documentation |
| [Google eng-practices: How to write comments](https://google.github.io/eng-practices/review/reviewer/comments.html) | "Nit:" for non-blocking; comment on code not person; explain the why; never just point, provide guidance |
| [Google eng-practices: The Standard](https://google.github.io/eng-practices/review/reviewer/standard.html) | Perfect is the enemy of good. Approve if it improves codebase health even with outstanding nitpicks |
| [Conventional Comments spec](https://conventionalcomments.org/) | Labeling system: `praise:`, `issue:`, `suggestion:`, `question:`, `nit:`, `thought:` + `(blocking)`/`(non-blocking)` decorators |
| [Dr. Michaela Greiler: Respectful code review feedback](https://www.michaelagreiler.com/respectful-constructive-code-review-feedback/) | 10 techniques: ask questions, I-messages, no sarcasm, no condescending words ("just"/"simply"), tailor to experience level |
| [Dr. Michaela Greiler: Teaching during reviews](https://www.michaelagreiler.com/teach-during-code-reviews/) | Teach as a peer, not an authority. "Here's something I've found useful" > "you should know this" |

---

## Rust-Specific Review

| Resource | Key Insight |
|----------|-------------|
| [Pull Panda: Rust Code Review Checklist](https://pullpanda.io/blog/rust-code-review-checklist) | Ownership, borrowing, `.clone()` smell, feature flag hygiene |
| [corrode.dev: Pitfalls of Safe Rust](https://corrode.dev/blog/pitfalls-of-safe-rust/) | Integer overflow debug/release split; `Path::join()` absolute path replacement; `#[derive(Debug)]` on sensitive types; `secrecy` crate |
| [Qovery: Common Mistakes with Rust Async](https://www.qovery.com/blog/common-mistakes-with-rust-async) | `std::sync::Mutex` across `.await`; RAII guards held across await; `spawn_blocking` not abortable |
| [Alice Ryhl: Async: What is blocking?](https://ryhl.io/blog/async-what-is-blocking/) | ~100µs rule; `spawn_blocking` for blocking I/O; `rayon` for CPU-bound (not spawn_blocking) |
| [Google Comprehensive Rust: Async Cancellation](https://google.github.io/comprehensive-rust/concurrency/async-pitfalls/cancellation.html) | Every `.await` is a cancellation point; `write_all()` is not cancellation-safe |
| [Tokio docs: select! macro](https://docs.rs/tokio/latest/tokio/macro.select.html) | Cancellation safety, biased polling starvation, loop+select future restarting |
| [Huon Wilson: Integer Overflow Myths](https://huonw.github.io/blog/2016/04/myths-and-legends-about-integer-overflow-in-rust/) | Debug panics, release wraps — the split that surprises people |
| [Effective Rust: Features](https://effective-rust.com/features.html) | Feature flags must be additive; feature-gated public struct fields; typos silently disable code |

---

## Error Handling

| Resource | Key Insight |
|----------|-------------|
| [Luca Palmieri: Error Handling in Rust](https://lpalmieri.com/posts/error-handling-rust/) | Errors serve two purposes: control flow and reporting. `thiserror` for library, `anyhow` for application. Log once at the handling site. |
| [Shakacode: thiserror vs anyhow](https://www.shakacode.com/blog/thiserror-anyhow-or-how-i-handle-errors-in-rust-apps/) | Decision rule: if callers need to `match` on the error type, `thiserror`. If it just propagates to a handler, `anyhow`. |
| [SQLx docs: query macro](https://docs.rs/sqlx/latest/sqlx/macro.query.html) | SQLx intentionally rejects `String` as a query parameter — making SQL injection hard by design |

---

## Security

| Resource | Key Insight |
|----------|-------------|
| [OWASP: Secure Code Review Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secure_Code_Review_Cheat_Sheet.html) | OWASP Top 10 from the reviewer's perspective |
| [OWASP: SSRF Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html) | IP blocklist ranges, DNS rebinding, redirect chain validation |
| [Clear Gate: SSRF with DNS Rebinding](https://www.clear-gate.com/blog/ssrf-with-dns-rebinding-2/) | Validate the resolved IP at connection time, not at URL parse time — DNS TTL can expire between the two |
| [oneuptime: Rust API Security](https://oneuptime.com/blog/post/2026-01-07-rust-api-security/view) | IDOR via missing `user_id` filter; rate limiting with `governor`; JWT validation checklist |

---

## Observability & Privacy

| Resource | Key Insight |
|----------|-------------|
| [Luca Palmieri: Zero to Production — Telemetry](https://lpalmieri.com/posts/2020-09-27-zero-to-production-4-are-we-observable-yet/) | `secrecy::Secret<T>` to prevent PII in log output; structured fields with `%` and `?` formatters |
| [tracing crate docs](https://docs.rs/tracing/latest/tracing/) | `instrument`, `skip`, structured field syntax |

---

## Adding New References

When a review uncovers a new pattern, bug class, or useful resource:
1. Add a row to the appropriate table above
2. If the checklist should be updated, open a PR to `reviewer.md` as well
3. Commit the reference with a note on what triggered adding it (e.g., "added after catching Mutex-across-await in meridian-fetcher Session 03")

---

*Last updated: 2026-03-01 — Session 03 (backend skeleton implementation)*
