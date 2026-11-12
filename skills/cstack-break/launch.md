# Launching adversaries and the arbiter

Each one needs its own fresh agent, on the model and effort in [config.md](config.md), in its
own checkout of the exact commit under review. Use the first route that works.

## In T3 Code

Call `orchestrator_capabilities` once per thread to confirm the models and their effort option
ids. Then start each agent with `delegate_task`:

- `task`: the filled-in brief.
- `target`: the model's `providerInstanceId` and `model`, and `options` with its effort option,
  for example `{"effort": "high"}` for Claude or `{"reasoningEffort": "high"}` for Codex.
- `isolation: "worktree"`, so it gets its own checkout of your current commit. Uncommitted
  edits stay out, which is why the change must be committed first.
- `role: "review"`, `mode: "async"`, and a new `clientRequestId` every time.

Start both adversaries in the same message. Then wait for T3's notifications: do not poll with
`task_status`, `sleep` or watchers. Every round and every dispute is a new `delegate_task`, never
a message to an earlier child (`t3_thread_send`). An agent you no longer need gets
`task_cancel`.

## In Claude Code without T3

- **Claude model:** the `Agent` tool with `model` and `effort` from config.md (for example
  `model: "fable"`, `effort: "high"`), `isolation: "worktree"`, and `run_in_background: false`.
  Claude Code puts these checkouts under `.claude/worktrees/` in the repo. Remove them after the
  round (`git worktree remove --force <path>`, then the empty `.claude/worktrees` folder if it
  was not there before), and never commit them.
- **Codex model:** create a checkout and run `codex exec` there in the background:

  ```sh
  wt="$STATE/wt/r<round>-<name>"
  git worktree add --detach "$wt" <head SHA>
  codex exec -C "$wt" -s workspace-write --ephemeral -m <model> \
    -c model_reasoning_effort="<effort>" -o "$STATE/wt/r<round>-<name>.out" - < brief.txt
  ```

  Read the reply from the `.out` file, then remove the checkout with `git worktree remove
  --force "$wt"`.

## In Codex without T3

- **Codex model:** your native subagent tool, if it can run the configured model and effort.
  Otherwise use `codex exec` as above.
- **Claude model:** create a checkout as above and run Claude Code there:

  ```sh
  (cd "$wt" && claude -p --model <model> --effort <effort> --permission-mode dontAsk \
    --allowedTools "Bash Read Grep Glob Write Edit" < brief.txt > "$STATE/wt/r<round>-<name>.out")
  ```

## When no route works

If you cannot run one of the configured models, say so plainly and ask the user what to do.
Never quietly run the loop with one family, or swap in a different model.
