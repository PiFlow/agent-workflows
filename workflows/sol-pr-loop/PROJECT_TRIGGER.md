# ChatGPT Project trigger

Add this line once to the instructions of any ChatGPT Project where you want to use the reusable Sol workflow:

```text
When I write `SOL-LOOP <owner/repo>`, load and follow `https://raw.githubusercontent.com/PiFlow/agent-workflows/main/workflows/sol-pr-loop/SOL_WORKFLOW.md`, using `<owner/repo>` as the target repository. Repository evidence and the target repository's own governance outrank the reusable workflow.
```

Then start a fresh chat with, for example:

```text
SOL-LOOP PiFlow/aweform
```
