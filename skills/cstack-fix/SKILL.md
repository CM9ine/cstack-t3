---
name: cstack-fix
description: Use when the user's message starts with or mentions "cstack-fix".
---

# cstack-fix

The rest of the user's message is the bug. Work through the steps in order. Stop for the user
only at the goal OK in step 1, and for a decision no experiment can settle. Everything else
you decide, and you list those decisions in the PR.

Every line you ship must trace back to evidence you saw. A change that "might help" is a
guess. Revert it.

## 1. Goal

Follow [the goal step](../cstack-break/goal.md): write the goal, wait for the user's OK, then
freeze it. No code changes before the OK.

## 2. See the bug happen

Reproduce it yourself, on the surface where the user sees it: run the test, call the endpoint,
drive the UI (T3's `preview_*` tools for web and `device_*` tools for iOS). Do
not reason from the code alone.

If it does not reproduce, narrow the conditions, build the trigger yourself, or add logging
until it does. Ask the user only when you have tried and can name what stops you, for example
production data you cannot reach.

## 3. Prove the cause

List the possible causes. Rule them out one at a time with evidence from running code: logs,
added instrumentation, a debugger, or `git bisect` when it used to work. Each check should rule
out as much as possible. Keep going until one cause is left, and confirm it by showing the
mechanism happening, not just that it is plausible.

## 4. Failing test, then the fix

1. Write a test that fails because of the bug and commit it on its own. If the repo has no
   test setup, or the bug can only be seen in the UI, commit a repro script or write the exact
   steps in `$STATE/proof.md` instead.
2. Fix the cause with the smallest change, and commit it separately.

The history then shows the bug existed and that this fix is what stopped it. Do not push yet.

## 5. Prove it

The failing test now passes, the original reproduction from step 2 no longer shows the bug, and
the repo's checks are green. Write the before and after output to `$STATE/proof.md`
([record.md](../cstack-break/record.md)).

## 6. Break loop

Follow [cstack-break](../cstack-break/SKILL.md) until a fresh round comes back clean.

## 7. PR

If `$STATE/ending` says `full-send`, or the user has said "full send" at any point, follow
[full-send](../full-send/SKILL.md). Otherwise follow [full-stop](../full-stop/SKILL.md).
