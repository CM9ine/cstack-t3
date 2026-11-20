---
name: full-send
description: Use when the user says "full send", at the start of a task or at any point during one.
---

# Full send

The user said "full send": they are not watching this thread, and they want the work taken all
the way to a merged PR. Your updates and your final message go unread until long after you
stop.

If the user did not clearly say "full send", do not assume this much autonomy.

## What it changes

- **In the middle of a cstack-build or cstack-fix run:** write `full-send` to the state
  folder's `ending` file ([record.md](../cstack-break/record.md)) and carry on with that run.
  Only its ending changes.
- **The goal OK still applies.** Full send never skips the user's OK on the goal. Once the goal
  is confirmed, the rest runs without them.
- **On its own:** follow [full-stop](../full-stop/SKILL.md) for an existing branch or PR, with
  the changes below.

## Permissions

The user grants you permission to:

- Merge the PR once the merge gate in [babysit.md](../full-stop/babysit.md) passes.
- Push the PR's own branch with `--force-with-lease` after a rebase.
- Use the test methods the proof needs without asking first: browser automation, simulators
  and devices, running the app or a dev server, screenshots. This overrides project rules that
  say to ask before testing, but not rules about *how* to test. It covers only the tests this
  task needs. It does not cover deleting data, touching production, or bypassing a harness
  approval prompt.

## Steps

Follow [full-stop](../full-stop/SKILL.md) with these changes:

1. Leave auto-merge and merge queues alone if the repo uses them.
2. Stay active until the PR is merged. If you end your turn while checks or bots are pending,
   the work stops, because nobody will prompt you again. Wait with the watcher from
   [babysit.md](../full-stop/babysit.md).
3. At the gate, merge instead of stopping:
   - Right before merging, run `git fetch` and check the PR again. Never merge when it is
     BEHIND or DIRTY, or when base commits that landed after the last green run touch this
     PR's files. Rebase, push, recheck, and wait for green again.
   - Use the merge method the repo uses: check the allowed methods and recent merged PRs.
   - Merge through branch protection as configured. Never use `--admin` or any other bypass.
     You may ignore automated reviews that only ask for "human review" when you are confident
     in the change.
   - If the repo uses a merge queue or auto-merge, stay until GitHub reports the PR merged.
4. Report in your final message: the PR link, the merge commit, the proof, the break-loop
   totals, anything escalated, and every decision you made for the user.

If you have to stop before the merge, call `unwatch_pull_request` first and ask as
[babysit.md](../full-stop/babysit.md) says. If no question tool works, leave the PR open and
unmerged, and start your final message with the question.
