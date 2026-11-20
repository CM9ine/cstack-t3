---
name: full-stop
description: Use when the user says "full stop", or when cstack-build or cstack-fix reach their PR step.
---

# Full stop

Take the change to a PR that is ready to merge, then stop and hand it to the user. You never
merge.

If the user has said "full send" at any point, or the state folder's `ending` file says
`full-send`, follow [full-send](../full-send/SKILL.md) instead. It uses these same steps and
merges at the end.

You may commit, push, rebase, fix conflicts, and push this task's branch with
`--force-with-lease`. Never merge, enable auto-merge, add the PR to a merge queue, push to the
default branch, bypass branch protection, or have anyone else do those things. If auto-merge
is already on, or the PR is in a merge queue, turn it off or take it out first, and say so in
your report.

## 1. Make sure the break loop ran

Find the state folder ([record.md](../cstack-break/record.md)). Every PR goes through the break
loop. If this branch has no record, follow [cstack-break](../cstack-break/SKILL.md) first: it
starts with the goal step, so the user confirms the goal before anything else. Then run
`../cstack-break/scripts/report check` and fix everything it lists.

## 2. File the PR

Follow [pr.md](pr.md). If a PR for this branch already exists, update it instead of opening
another one.

## 3. Babysit it

Follow [babysit.md](babysit.md) until the PR is ready, waiting on a human, or blocked.

## 4. Stop and hand off

First fetch the default branch and check the live PR: head, base, checks, reviews and
mergeability. Confirm it is open, not a draft, and not set to merge on its own. If someone else
merged it, report that instead.

- **Ready:** every point of the merge gate in [babysit.md](babysit.md) holds. Leave the PR open.
- **Waiting on a human:** only human approval is left. Confirm that is the real blocker.
- **Blocked:** say what stops it, what you tried, and what is needed.

Call `unwatch_pull_request` before you hand off or ask a question. A watched thread
stays in "Working" and out of the user's inbox.

Report: the PR link, the final head SHA, the state (ready, waiting or blocked), how the change
was proven, the break-loop totals, anything escalated, and decisions you made for the user.
