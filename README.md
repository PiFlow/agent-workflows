# PiFlow Agent Workflows

Reusable, project-agnostic workflows for the **Sol ↔ Luna PR lifecycle**.

## Components

- `skills/luna-pr-loop/SKILL.md` — Codex Luna Manager skill.
- `workflows/sol-pr-loop/SOL_WORKFLOW.md` — ChatGPT GPT-5.6 Sol architect/reviewer workflow.

Project-specific rules stay in each target repository (`AGENTS.md`, ADRs, policies, task issues, tests and CI). This repository contains only reusable lifecycle machinery.

## Luna Manager

Install:

```bash
mkdir -p ~/.codex/skills/luna-pr-loop
curl -fsSL \
  https://raw.githubusercontent.com/PiFlow/agent-workflows/main/skills/luna-pr-loop/SKILL.md \
  -o ~/.codex/skills/luna-pr-loop/SKILL.md
```

Start a new Codex chat and invoke:

```text
$luna-pr-loop
```

or:

```text
$luna-pr-loop issue #123
```

## Sol workflow

For a ChatGPT Project, add this one-time Project instruction:

```text
When I write `SOL-LOOP <owner/repo>`, load and follow `https://raw.githubusercontent.com/PiFlow/agent-workflows/main/workflows/sol-pr-loop/SOL_WORKFLOW.md`, using `<owner/repo>` as the target repository. Repository evidence and the target repository's own governance outrank the reusable workflow.
```

Then a fresh Sol chat can start with only:

```text
SOL-LOOP PiFlow/aweform
```

or:

```text
SOL-LOOP PiFlow/ausplant
```

## Lifecycle

1. Sol reconstructs the exact repository state.
2. Sol and the user agree the next bounded task.
3. Sol creates a durable GitHub issue containing `LUNA_MANAGER_AUTHORIZATION`.
4. Sol starts the hourly ChatGPT PR-review watcher.
5. Luna Manager discovers the issue with `$luna-pr-loop`.
6. Fresh Luna workers implement/correct and post `[LUNA-HANDOFF]`.
7. Sol independently reviews each genuinely new exact HEAD and posts `[SOL-HANDOFF]` or `[SOL-PASS]`.
8. Additional independent review is obtained when repository governance requires it.
9. Merge occurs only after all required gates and explicit user authorization.
10. The watcher is disabled and no successor task is implied.

## Authority order

1. Explicit current user instruction.
2. Target repository governance and accepted durable records.
3. Task-specific GitHub authorization issue.
4. Exact GitHub PR / commit / CI / review state.
5. Reusable workflow defaults.
6. Conversational summaries.

The reusable workflows never silently weaken stricter target-repository governance.
