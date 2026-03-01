# Research Agent

**Type:** `general-purpose`
**Mode:** Foreground (returns findings before parent continues)

## Purpose

Deep research on a topic — web searches, documentation fetches, codebase searches — synthesized into structured notes the parent session can act on.

## Prompt Template

```
Research [TOPIC] and return structured notes covering:

1. [QUESTION_1]
2. [QUESTION_2]
3. [QUESTION_3]

Search for: "[SEARCH_TERM_1]", "[SEARCH_TERM_2]"

Return notes organized by category. Be thorough — this will be used to [PURPOSE].
```

## Example Usage

See the Session 03 Meridian example: research agent used to gather expert code review practices across 6 categories (general best practices, Rust-specific, security, privacy, performance, communication style).
