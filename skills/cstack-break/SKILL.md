---
name: cstack-break
description: Use when the user says "cstack-break", or when another cstack skill reaches its break step.
---

# cstack-break

You are the developer. Fresh adversaries, one from each model family in
[config.md](config.md), try to break the change. You fix what they prove. A new set of fresh
adversaries, who know nothing about earlier rounds, then gets the identical brief. The loop
ends when a fresh round finds nothing real.

| Role | Memory | Job |
| --- | --- | --- |
| Developer (you, the main thread) | Keeps context | Reruns every repro, fixes, keeps the record |
| Adversaries (all in config.md) | Fresh every round | Break the change and prove each finding |
| Arbiter (config.md) | Fresh per dispute | Settles findings you dispute |

You never judge your own work alone. A finding that reproduces is fixed, or it goes to the
arbiter. You may not drop it, downgrade it, or call it out of scope on your own.

## Before round 1

1. **Goal.** Find the state folder ([record.md](record.md)). If `$STATE/goal.md` is missing, run
   the goal step in [goal.md](goal.md) first, including waiting for the user's OK.
2. **Scope.** Run `git fetch origin`. The base is `git merge-base HEAD origin/<default branch>`,
   or the PR's base branch if a PR exists. With no remote, use the local default branch. The
   change is base to `HEAD`. Leave unrelated work
   out: do not stash or discard it. If unrelated changes are mixed into the same files, ask the
   user.
3. **Checks.** Run the checks the repo's CI runs for the touched code (typecheck, lint, tests).
   Fix failures first. Never send adversaries code that fails its own checks.
4. **Commit.** Commit everything in the change, so adversaries can check out one exact commit.
   Do not push during the loop.

## A round

1. **Record.** Append the round to `rounds.tsv` with the head SHA and diff size. Fill in
   `adversaries` once all have replied.
2. **Launch** all adversaries at the same time, fresh, with the brief in
   [adversary.md](adversary.md). [launch.md](launch.md) says how in each harness. Change no code
   while they run.
3. **Collect** every reply. If one fails or returns garbage, launch a fresh one with the same
   brief. Never count a failed adversary as a clean result.
4. **Triage.** Log each finding in `findings.tsv` first, then:
   - **Merge duplicates.** Findings with the same cause become one row, with `by` naming
     every adversary that reported it.
   - **Filter repeats.** A finding the arbiter already closed in an earlier round is
     `filtered`, naming the earlier id. Only arbiter-closed findings are filtered. A finding
     you fixed earlier that is back is new: log it with `reopens` set.
   - **Reproduce** every blocker, should and nit in your own checkout, using the repro and
     files the adversary gave. A finding that does not reproduce is `dropped`.
5. **Resolve** what survived:
   - **Blocker or should:** fix it with the smallest change that makes the repro pass. Add the
     repro as a test when the repo has a test setup. One commit per finding, with its id in the
     commit message, then `fixed` with the commit. If you think it is wrong, out of scope, or a
     lower severity, it is a dispute: send it to the arbiter with [arbiter.md](arbiter.md).
     `FIX` means fix it (`arbiter` = `finding-upheld`). `CLOSE` means `closed`
     (`arbiter` = `dispute-upheld`).
   - **Overbuild flagged by one adversary:** remove the code (`removed`), or keep it with a
     one-line reason (`kept`). The reason goes in the PR.
   - **Overbuild flagged by two or more adversaries on the same code in one round:** treat it
     as a should. Remove it, or dispute it with the arbiter.
   - **Nit:** `noted`. You may fix a nit that changes no behavior (a comment, a name, a doc)
     and mark it `fixed`. Never change behavior for a nit. List the rest as follow-ups.
   - **Concern:** `noted`. If it is about security, auth, data loss or permissions, try to
     reproduce it yourself. If it reproduces, it becomes a blocker or should. If not, mark it
     `escalated`, so it is flagged at the top of the PR. The loop does not wait for it.
6. **Check your fixes.** Run the checks again after fixing. If a fix grew the diff, look for a
   smaller one before the next round.
7. **Decide.** The loop is done when this round's fresh adversaries produced no blocker or
   should that you fixed or that is still open, and you changed no behavior code in this round.
   Otherwise start the next round.

There is no round limit. Keep going until a round is clean. `scripts/report` notes stall
signals in the PR (a fix that did not hold, proven findings that stop dropping, a diff that
keeps growing) but nothing stops the loop. Each round, ask yourself whether your fixes are
creating the next round's findings. If they are, make smaller fixes.

## When the loop is done

Run `scripts/report check` from this skill's folder and fix everything it lists. Then:

- Called from `cstack-build` or `cstack-fix`: go back to that skill's next step.
- Called by the user on its own: show the `scripts/report` output and stop. Do not open a PR
  unless the user asks for one or says "full stop" or "full send".

## Post-PR rounds

`full-stop` calls this after the PR is open, when babysitting changed the code. It says which
kind:

- **`post-pr-small`** (a CI fix, a review-bot finding, a conflict resolution): exactly one round
  with all adversaries. Fill in the brief's "Focus" line with the previous reviewed head, which
  is the last `head` in `rounds.tsv`. Fix what it proves, as in step 5, with no second round.
- **`post-pr-full`** (new behavior or a big change): the normal loop over the whole change,
  until a round is clean.

Round numbers continue from the existing record. Afterwards `full-stop` refreshes the PR
section.
