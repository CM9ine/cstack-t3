# Launching adversaries and the arbiter

Each one needs its own fresh agent, on the model and effort in [config.md](config.md), in its
own checkout of the exact commit under review. cstack always runs in a T3 Code thread, so
every model, Claude, Codex and Gemini alike, starts through T3.

Call `orchestrator_capabilities` once per thread to confirm the models and their effort option
ids. Then start each agent with `delegate_task`:

- `task`: the filled-in brief.
- `target`: the model's `providerInstanceId` and `model`, and `options` with its effort option,
  for example `{"effort": "high"}` for Claude, `{"reasoningEffort": "high"}` for Codex, or
  `{"thought_level": "medium"}` for Gemini (provider `antigravity`).
- `isolation: "worktree"`, so it gets its own checkout of your current commit. Uncommitted
  edits stay out, which is why the change must be committed first.
- `role: "review"`, `mode: "async"`, and a new `clientRequestId` every time.

Start all adversaries in the same message. Then wait for T3's notifications: do not poll with
`task_status`, `sleep` or watchers. Every round and every dispute is a new `delegate_task`, never
a message to an earlier child (`t3_thread_send`). An agent you no longer need gets
`task_cancel`.

## When a model cannot run

If `delegate_task` cannot run one of the configured models (the provider is disabled, or the
model is missing from `orchestrator_capabilities`), say so plainly and ask the user what to do.
Never quietly run the loop without one of the configured models, or swap in a different
model.
