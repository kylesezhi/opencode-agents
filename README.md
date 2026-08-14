---
# This file is documentation, NOT an opencode agent.
#
# opencode registers every *.md file under ~/.config/opencode/agents/ as an
# agent (the filename becomes the agent name). `disable: true` keeps this
# README out of the agent roster — do not remove it, or a phantom "readme"
# agent will appear in `opencode agent list`.
# See https://opencode.ai/docs/agents/
disable: true
---

# opencode agents

Custom agents for [opencode](https://opencode.ai), installed globally at
`~/.config/opencode/agents/`. They implement a three-role software pipeline:

| File | Agent | Role |
|------|-------|------|
| [`architect.md`](architect.md) | `architect` (primary) | Interviews the user, decomposes work into tasks, delegates, adjudicates |
| [`developer.md`](developer.md) | `developer` (subagent) | Implements concrete tasks, runs tests, never commits |
| [`reviewer.md`](reviewer.md) | `reviewer` (subagent) | Adversarially reviews implementations; read-only |

## How it works

- The Architect owns requirements and architecture. It runs a strictly
  sequential pipeline: define task → delegate to Developer → send the result
  to Reviewer → pass/correct until approved → next task.
- The Developer writes code within the assigned scope and reports back. It may
  use read-only git commands but never stages, commits, or pushes.
- The Reviewer returns `PASS` or `CHANGES_REQUIRED` with findings. It never
  modifies application code. The Architect resolves disagreements.

## Adding an agent

Create a new `.md` file in this directory with YAML frontmatter. The filename
(without `.md`) becomes the agent name.

```markdown
---
description: What this agent does and when to use it
model: provider/model
mode: subagent        # or "primary"
permission:
  edit: deny
  bash: allow
---

You are ... (system prompt body)
```

`description` and `mode` are required for it to be usable; `model` selects the
LLM. See https://opencode.ai/docs/agents/ for all options.

## Gotchas

- **Every `.md` file here becomes an agent.** Including this README — hence
  the `disable: true` frontmatter. If you add other documentation, give it
  the same treatment or use a non-`.md` filename.
- The directory is its own git repo (`kylesezhi/opencode-agents`) — commit
  agent changes here, independently of any project.
