---
name: luna-pr-loop
description: Run the reusable Luna Manager lifecycle for a GitHub-authorized implementation PR. Use when the user invokes $luna-pr-loop, says LUNA-LOOP, asks to start or resume the Luna manager, or wants the established Sol↔Luna PR loop. Discover the durable GitHub authorization issue, verify its exact base, launch fresh ephemeral Luna workers for implementation or bounded corrections, track one PR and exact HEAD, react only to valid [SOL-HANDOFF] and [SOL-PASS] review markers, and never implement or merge as the manager.
compatibility: Designed for OpenAI Codex with git, GitHub access (normally gh), and codex exec available.
metadata:
  author: PiFlow
  version: "1.0"
  short-description: Reusable GitHub-authorized Luna Manager PR loop
---

# Luna PR Loop

## Purpose

Act only as the **Luna Manager** for one GitHub-authorized implementation lifecycle.

The manager owns routing, state, fresh-worker launches, PR/head tracking, and correction dispatch. The manager does **not** implement project changes, perform independent scientific/product review, merge, or begin a later task.

The user's explicit current instruction takes precedence over this skill. Task-specific repository governance and the durable GitHub authorization must still be respected unless the user explicitly changes that governance through the repository's accepted process.

## Invocation

Preferred explicit invocation:

```text
$luna-pr-loop
```

Useful forms:

```text
$luna-pr-loop issue #123
$luna-pr-loop resume
LUNA-LOOP
```

If the user supplies an issue number, use that issue. Otherwise discover the current authorization as described below.

## Non-negotiable role separation

### Manager may

- inspect repository/GitHub state;
- read applicable `AGENTS.md`, governance, issue, PR, CI, and review comments;
- maintain local manager state under the repository git-common-dir;
- launch a fresh ephemeral Luna worker using the exact worker mechanism authorized by the issue;
- create or update a local Codex polling automation when available;
- verify worker handoffs and route bounded Sol-requested corrections.

### Manager must not

- edit project files itself;
- substitute its own implementation for a fresh worker;
- silently change scientific/product scope;
- independently approve its own worker's work;
- launch or impersonate Sol or another independent reviewer;
- merge;
- enable auto-merge;
- begin the next task;
- silently rebase an authorization whose exact base no longer matches;
- treat tests, CI, or Luna self-review as an independent approval.

## Authority order

For task facts, use this order:

1. explicit current user instruction;
2. current repository governance/instructions (`AGENTS.md`, accepted policy/ADR/docs);
3. the selected durable GitHub authorization issue;
4. exact GitHub PR/commit/CI/comment state;
5. local manager state;
6. conversational summaries.

Repository and GitHub evidence outrank agent summaries.

## Phase 1 — identify the repository

1. Resolve the git root with `git rev-parse --show-toplevel`.
2. Resolve the git common directory with `git rev-parse --git-common-dir`.
3. Resolve `origin` and infer the GitHub `owner/repo`.
4. Read all applicable `AGENTS.md` / `AGENTS.override.md` files before routing work.
5. Inspect current branch, HEAD, worktree status, and `origin/main`.
6. Do not modify the user's current worktree to prepare the implementation.

If there is no Git repository or no usable GitHub remote, stop with a concise diagnostic.

## Phase 2 — find the durable authorization

If the user named an issue, fetch it.

Otherwise search open issues in the current repository for the marker:

```text
LUNA_MANAGER_AUTHORIZATION
```

Prefer the newest issue that is clearly active and whose repository matches the current repository.

If there are multiple plausible active authorization issues, list only their issue numbers/titles/task IDs and ask the user which one to run. Do not guess.

The authorization must contain concrete values for, at minimum:

```text
TASK_ID
REPOSITORY
BASE_HEAD
ISSUE
EXISTING_BRANCH
EXISTING_PR
BRANCH_NAME
FRESH_WORKER_MECHANISM_EDIT
SOL_TASK_BLOCK_BEGIN ... SOL_TASK_BLOCK_END
GOVERNANCE_SUMMARY_BEGIN ... GOVERNANCE_SUMMARY_END
```

Use `MANAGER_RULES` when present.

If required fields are placeholders, missing, contradictory, or refer to another repository, stop and report the exact defect. Do not invent values.

## Phase 3 — exact-base preflight

Before the **first** implementation worker for the task:

1. fetch the relevant remote refs;
2. verify the repository matches `REPOSITORY`;
3. verify current authoritative `origin/main` (or the issue-declared authoritative base ref) is exactly `BASE_HEAD`;
4. verify the authorized branch/PR state is consistent with `EXISTING_BRANCH` / `EXISTING_PR`;
5. verify no prior manager state shows this task already completed.

If `main` has moved away from `BASE_HEAD` before the first worker begins:

- do not silently rebase;
- do not reinterpret the authorization;
- do not launch a worker;
- report the actual current main SHA and authorized base SHA and wait for renewed authorization.

After a task PR exists, follow that PR's exact current head rather than requiring `main` to remain frozen, unless the authorization says otherwise.

## Phase 4 — local lifecycle state

Store manager-only state outside the committed worktree under:

```text
<git-common-dir>/sol-luna-loop-state/
```

Use a task file such as:

```text
task-<TASK_ID>.json
```

Track at least repository, task ID, authorization issue, base SHA, branch, PR number, latest known PR HEAD, latest `[LUNA-HANDOFF]` HEAD, latest Sol-reviewed HEAD, last processed Sol handoff/comment identifier, correction round count, lifecycle state, and timestamps when useful.

Never commit this manager state.

Lifecycle states should be simple:

```text
AUTHORIZED
WORKER_RUNNING
AWAITING_SOL
CORRECTION_REQUIRED
SOL_PASSED
BLOCKED
```

## Phase 5 — launch the first fresh worker

The manager never implements.

Use the exact `FRESH_WORKER_MECHANISM_EDIT` from the authorization issue. Do not replace its model, sandbox, or ephemerality silently.

The worker prompt must include:

1. the complete `SOL_TASK_BLOCK` **verbatim**;
2. the repository root and authorization issue number/URL;
3. instruction to inspect the actual repository before editing;
4. instruction to obey repository `AGENTS.md` and task governance;
5. instruction to work only on the authorized task/branch/base;
6. instruction to test/validate as required by the issue;
7. instruction to commit and push only its authorized work;
8. instruction to open/update exactly the authorized PR;
9. instruction to post the required `[LUNA-HANDOFF]`;
10. instruction to terminate after handoff;
11. explicit prohibition on merge and later-task work.

A worker is **fresh** only if it is a new ephemeral worker process with no implementation/correction conversation history inherited from a prior worker.

## Phase 6 — verify the worker handoff

When the worker finishes:

1. inspect GitHub directly;
2. determine the actual PR number, base, branch, and exact current 40-character HEAD;
3. find the latest valid `[LUNA-HANDOFF]`;
4. verify the handoff names the exact current HEAD;
5. inspect whether required GitHub CI/checks exist and their current state;
6. update manager state to `AWAITING_SOL`.

Do not turn this verification into an independent acceptance review. The manager may detect routing/state defects, but it must not replace Sol.

If the worker failed to create a valid handoff/PR, report the failure. Do not manufacture a handoff.

## Phase 7 — watch for Sol

The independent reviewer is outside this skill.

Actionable review markers are:

```text
[SOL-HANDOFF]
[SOL-PASS]
```

Treat a review as applicable only when it clearly identifies the exact current PR HEAD, or the correction request is unambiguously tied to that current candidate under the repository's established review protocol.

Ignore Luna self-review as independent evidence.

### On `[SOL-PASS]`

Require all of the following:

- reviewer identifies itself as independent Sol / the repository-authorized reviewer role;
- verdict is PASS;
- reviewed SHA exactly equals the current PR HEAD;
- no newer commit exists.

Then:

1. set state `SOL_PASSED`;
2. record the reviewed SHA;
3. stop the Luna correction lifecycle;
4. disable any local Luna-manager polling automation for this task;
5. report that the candidate awaits whatever additional review and explicit user/Flow merge authorization repository governance requires.

Do not merge.

### On `[SOL-HANDOFF]` / CHANGES_REQUIRED

Before acting:

1. verify it is new and not already processed;
2. verify it applies to the current candidate;
3. verify it contains bounded correction instructions;
4. increment the correction round.

Maximum correction rounds: **4**, unless the authorization issue explicitly sets a stricter limit.

If the limit is exceeded, set `BLOCKED` and return to the user.

For each correction, launch a **new fresh ephemeral Luna worker**. Never reuse the previous worker.

The correction worker receives the original authorization issue and complete `SOL_TASK_BLOCK`, current PR number and exact current HEAD, the exact Sol correction text, instruction to make only those bounded corrections, all unchanged governance constraints, required validation, instruction to commit/push and post a new `[LUNA-HANDOFF]`, and prohibition on merge or later-task work.

After the correction worker finishes, verify the new handoff/HEAD and return to `AWAITING_SOL`.

Any new commit invalidates an earlier SHA-specific PASS.

## Phase 8 — polling automation

If the current Codex environment exposes its local automation/scheduling facility, create one task-scoped poll after the implementation PR exists.

Preferred cadence:

```text
every 15 minutes
```

The poll must:

- inspect only the authorized PR;
- load this task's manager state;
- no-op when there is no new actionable Sol marker;
- route one new correction at most once;
- stop/disable after exact-current-HEAD `[SOL-PASS]`, task block, PR closure/merge, or explicit user stop;
- never merge or start another task.

If local Codex scheduling is unavailable, **do not** simulate a background watcher with an endless `sleep` loop. Preserve state and tell the user that `$luna-pr-loop resume` will perform the next lifecycle check.

ChatGPT scheduled review/watchers are separate from this manager and are not created by the Luna skill.

## Existing PR / resume behavior

When `EXISTING_PR` is concrete or local state already contains a PR:

1. do not launch a new initial implementation worker;
2. inspect the exact current PR state;
3. reconcile manager state from GitHub;
4. process only a new actionable Sol marker;
5. otherwise report `AWAITING_SOL`.

When a PR is already merged or closed, stop. Do not infer or begin a successor task.

## Safety against duplicate work

Before launching any worker:

- confirm no worker for the same lifecycle action is already running if that can be determined;
- confirm the relevant Sol correction has not already been processed;
- confirm the PR HEAD has not already advanced because another worker handled it;
- update local state before and after launches.

Never intentionally launch multiple concurrent implementation/correction workers against the same task.

## Output contract

Keep manager messages compact.

After startup/preflight, report:

```text
LUNA PR LOOP
Repository: <owner/repo>
Task: <TASK_ID>
Issue: #<n>
Base: <BASE_HEAD>
PR: <number or NONE>
State: <state>
Next action: <one line>
```

After worker handoff, include the exact PR HEAD.

After Sol PASS, report:

```text
SOL PASS CONFIRMED
PR: #<n>
Exact HEAD: <sha>
Manager lifecycle: stopped
Merge: NOT PERFORMED
Next: awaiting repository-required additional review and explicit user/Flow authorization
```

## Final checks before every manager action

Before launching a worker or changing lifecycle state, confirm:

- correct repository;
- correct authorization issue;
- correct task ID;
- correct exact base/current PR HEAD;
- correct branch/PR;
- applicable repository instructions loaded;
- no duplicate action;
- no unauthorized scope expansion;
- manager is not implementing;
- manager is not merging;
- manager is not launching an independent reviewer;
- a fresh worker is used for each implementation/correction;
- exact-SHA review semantics are preserved.

When uncertain about a project-defining ambiguity, stop and ask the user rather than silently inventing policy.
