# claude-agents

True autonomous agents for Claude Code — prompt templates and orchestration patterns for the `Task` tool.

Unlike slash command skills (see [claude-skills](https://github.com/saldistefano/claude-skills)), these are **subprocesses** — independent Claude instances that run in parallel with their own context window and return results to the parent session. The parent can spawn many agents simultaneously and work on other things while they run.

---

## How True Agents Work

In Claude Code, the `Task` tool spawns a subprocess:

```
Parent Claude (your session)
  ├── Task → research-agent     (runs independently, parallel)
  ├── Task → bash-agent         (runs independently, parallel)
  └── Task → explore-agent      (runs independently, parallel)
        ↓ (all return results)
Parent resumes with combined output
```

Each agent:
- Gets its own isolated context window
- Cannot see the parent conversation
- Has access to a defined set of tools
- Returns a single message to the parent when done
- Can be run in the background (non-blocking)

---

## Built-in Agent Types

Claude Code provides these agent types as `subagent_type` values:

| Type | Tools | Best for |
|------|-------|---------|
| `general-purpose` | All tools | Research, multi-step tasks, code search across many files |
| `Bash` | Bash only | Git operations, running commands, terminal tasks |
| `Explore` | Read, Glob, Grep, Bash, WebFetch (no Edit/Write) | Fast codebase exploration, answering "how does X work?" |
| `Plan` | Read, Glob, Grep, Bash, WebFetch (no Edit/Write) | Designing implementation plans before writing code |
| `claude-code-guide` | Glob, Grep, Read, WebFetch, WebSearch | Questions about Claude Code features, API, SDK |

---

## Agent Templates

Reusable prompt templates for common autonomous tasks. Each template is designed to be passed as the `prompt` parameter to the `Task` tool.

See the [`agents/`](agents/) directory.

---

## Orchestration Patterns

### Pattern 1: Parallel Research

Launch multiple independent research agents simultaneously, collect results:

```
research-agent-1: "Search for X across the codebase"
research-agent-2: "Fetch and summarize documentation for Y"
research-agent-3: "Find all usages of pattern Z"
          ↓ (all run in parallel)
parent: synthesize findings
```

**When to use:** Exploratory tasks where multiple independent questions need answers. Research that would take 3+ sequential searches in the parent context.

### Pattern 2: Background Build Validation

Spawn a build/test agent in the background while doing other work:

```
background: "Run cargo test --workspace and return results"
parent:     (continues writing code while tests run)
          ↓ (background agent completes)
parent: reads results, addresses failures
```

**When to use:** Long-running validation that doesn't need to block the main session.

### Pattern 3: Parallel File Generation

Generate multiple independent files simultaneously:

```
agent-1: "Write the README for crate X"
agent-2: "Write the README for crate Y"
agent-3: "Write the README for crate Z"
          ↓ (all run in parallel)
parent: verify outputs, commit
```

**When to use:** Creating multiple independent artifacts that don't depend on each other.

### Pattern 4: Specialised Research + Implementation

One agent researches, parent implements based on findings:

```
research-agent: "Research best practices for X, return structured notes"
          ↓ (agent completes)
parent: reads notes, writes implementation
```

**When to use:** Tasks that require external knowledge before implementation can begin.

---

## When to Use True Agents vs Skills

| Use a **skill** (`/command`) | Use a **true agent** (`Task` tool) |
|------------------------------|-------------------------------------|
| You want a persona to advise you interactively | You need autonomous, non-interactive work done |
| The task involves back-and-forth conversation | The task is well-defined and can run to completion |
| You want to guide the process | You want to delegate and get results back |
| One task at a time | Multiple tasks running in parallel |
| Needs the full conversation context | Gets a fresh, isolated context |

**Rule of thumb:** If you'd ask a colleague to do something and come back with the answer, use an agent. If you'd sit down with a consultant to think through something together, use a skill.

---

## Repository Structure

```
claude-agents/
├── README.md                    ← this file
├── agents/                      ← reusable prompt templates
│   ├── research.md              ← general research agent
│   ├── codebase-explorer.md     ← deep codebase analysis
│   ├── security-audit.md        ← security review subprocess
│   ├── build-validator.md       ← CI validation agent
│   └── doc-writer.md            ← documentation generation
└── patterns/                    ← orchestration pattern examples
    ├── parallel-research.md
    ├── background-validation.md
    └── parallel-generation.md
```

---

## Related

- [claude-skills](https://github.com/saldistefano/claude-skills) — slash command personas (`/architect`, `/reviewer`, `/coo`, etc.)
- [Claude Code Task tool documentation](https://docs.anthropic.com/en/docs/claude-code)
