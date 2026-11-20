# Working on cstack

This repo holds Chris-Jay's agent skills. `bin/cstack` symlinks them into Claude Code and Codex
on every machine, so a change here reaches every harness on the next `cstack update`. Read
[README.md](README.md) for what the skills do.

## Skill conventions

- One folder per skill: `skills/<name>/SKILL.md`, kebab-case, with the folder name equal to the
  frontmatter `name`. Detail that is needed only at one step goes in its own file in the folder,
  linked from SKILL.md. Scripts go in `scripts/`.
- The `description` says only when to use the skill, in one short sentence with the trigger
  phrase, for example: Use when the user's message starts with or mentions "cstack-build".
  What the skill does goes in the body.
- Skills refer to each other by relative path (`../cstack-break/SKILL.md`). That works because
  every skill is linked into the same folder.
- Skills always run in a T3 Code thread, driven by Claude Code or Codex. Use T3 tools directly,
  with no fallback for running outside T3. Where Claude Code and Codex differ (such as the
  question tool), name both.
- Shared state goes in the record files described in `skills/cstack-break/record.md`, never in
  the conversation alone.
- Model names live only in `skills/cstack-break/config.md`.
- Write for an agent reading cold: short instructions, the reason when it is not obvious, no
  history of how a rule came about.

## Adapted material

`upstream.tsv` lists what was adapted from tstack, with the commit it came from.
`bin/cstack upstream` shows what changed upstream since. When you take upstream changes, update
the commit in `upstream.tsv`.

## Changing the installer

`bin/cstack` runs on Debian (`hub`) and stock macOS, so keep it to bash 3.2: no associative
arrays, no `mapfile`, no `readlink -f`. It must never replace a file it did not create. After a
change, run `bin/cstack install` twice (the second run should print only the summary line) and
`bin/cstack status`.

## Machines

How each machine is set up is in the fleet repo (`github.com/CM9ine/fleet`). When the install
steps change, update its `standard-box.md` and host files too.
