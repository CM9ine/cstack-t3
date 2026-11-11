---
name: cstack-build
description: Use when the user's message starts with or mentions "cstack-build".
---

# cstack-build

The rest of the user's message is the feature. Work through the steps in order. Stop for the
user only at the goal OK in step 1, and for a decision no experiment can settle. Everything
else you decide, and you list those decisions in the PR.

## 1. Goal

Follow [the goal step](../cstack-break/goal.md): write the goal, wait for the user's OK, then
freeze it. No code changes before the OK.

## 2. Understand the code

Learn how the parts the feature touches work today: the data they use, who calls them, and
the patterns the codebase already uses for this kind of thing (how settings are stored, how
screens are built, how errors are handled). The feature should look like the code around it.
For a wide search, hand it to a subagent and keep only its summary.

## 3. Decide the data shape

Before writing logic, write down the data: what gets stored, passed and returned, and the
structure that holds it. Prefer one structure that makes wrong states impossible over several
flags that can disagree: one `theme: "light" | "dark" | "system"`, not `isDark` plus
`followSystem`. State it in your reply, and put it in the PR description.

## 4. Build it

Build the smallest version that meets the goal, following the patterns from step 2. Add tests
for what users of the code can see, not for its internals. Commit in small, logical commits.
Do not push yet.

If the feature adds or restyles UI, follow [frontend-design](../frontend-design/SKILL.md). Where
the app already has a design system, keep to it and use the skill only for what it leaves open.

## 5. Prove it

Run the repo's checks. Then show the feature working on the real surface, as a user would see
it. Green tests alone are not proof.

- **Web UI:** in T3 Code, the `preview_*` browser tools. Elsewhere, whatever browser automation
  or screenshot tool the harness has.
- **iOS or macOS app:** in T3 Code, the `device_*` tools. Elsewhere, whatever simulator or
  device tooling the harness has.
- **CLI, server or library:** run the command or call the endpoint and capture the output.

Write the commands, outputs and screenshot paths to `$STATE/proof.md` ([record.md](../cstack-break/record.md)).
If you cannot prove it, say so and ask the user how to prove it. Do not move on unproven.

## 6. Break loop

Follow [cstack-break](../cstack-break/SKILL.md) until a fresh round comes back clean.

## 7. PR

If `$STATE/ending` says `full-send`, or the user has said "full send" at any point, follow
[full-send](../full-send/SKILL.md). Otherwise follow [full-stop](../full-stop/SKILL.md).
