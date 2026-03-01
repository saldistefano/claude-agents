# Build Validator Agent

**Type:** `Bash`
**Mode:** Background (non-blocking — parent continues while tests run)

## Purpose

Run build, test, and lint commands and return results. Useful for long-running CI checks that shouldn't block the main session.

## Prompt Template

```
Run the following commands and return all output including exit codes:

1. cargo build --workspace --features [FEATURES]
2. cargo clippy --workspace --features [FEATURES] -- -D warnings
3. cargo test --workspace --features [FEATURES]

Return: pass/fail per command, full error output for any failures, summary count of tests passed.
```
