# The record

Everything the cstack skills need to remember lives in files, not in the thread, so it survives
context compaction and works the same in every harness. The files sit inside the repo's git
directory, so they are never committed and each worktree has its own.

```sh
STATE="$(git rev-parse --absolute-git-dir)/cstack/$(git branch --show-current | tr '/' '-')"
mkdir -p "$STATE"
```

`scripts/report dir` prints the same path. Create the branch before you create the folder,
because the folder is named after the branch.

| File | Written by | Holds |
| --- | --- | --- |
| `goal.md` | the goal step ([goal.md](goal.md)) | The confirmed goal. Frozen: only the user can change it. |
| `ending` | the goal step, and `full-send` | One word: `full-stop` or `full-send`. |
| `proof.md` | `cstack-build`, `cstack-fix` | How the change was proven: commands, outputs, screenshot paths. |
| `rounds.tsv` | `cstack-break` | One row per round. |
| `findings.tsv` | `cstack-break` | One row per finding. |

## rounds.tsv

Tab-separated, with this header row:

```
round	kind	adversaries	base	head	added	removed	started	note
```

- `round`: 1, 2, 3… Never reused, also across post-PR rounds.
- `kind`: `loop`, `post-pr-small` or `post-pr-full`.
- `adversaries`: the names from [config.md](config.md) that ran this round, comma-separated
  (`Fable,Sol`). Fill it in once both replied, including adversaries that found nothing.
- `base`, `head`: full SHAs. `head` is the commit the adversaries reviewed.
- `added`, `removed`: from `git diff --numstat <base> <head>`, summed over the change.
- `started`: ISO time. `note`: anything unusual (a goal change the user made, a failed launch).

## findings.tsv

Tab-separated, with this header row:

```
id	round	by	severity	location	claim	repro	status	commit	arbiter	reopens	note
```

- `id`: `r<round>-<n>`, such as `r2-3`.
- `by`: who found it, using the adversary names in [config.md](config.md), joined with `+` when
  both did (`Fable+Sol`).
- `severity`: `blocker`, `should`, `nit`, `overbuild` or `concern`.
- `location`: `file:line`. `claim`: one sentence. `repro`: the one-line command, or empty.
- `status`, one of:
  - `fixed`: changed in `commit`.
  - `removed`: overbuilt code deleted in `commit`.
  - `kept`: overbuilt code kept, with the reason in `note`.
  - `dropped`: did not reproduce when you reran it.
  - `closed`: the arbiter agreed with your dispute.
  - `filtered`: a repeat of a finding the arbiter already closed. Name it in `note`.
  - `noted`: a nit or concern recorded and not acted on.
  - `escalated`: a security, auth, data-loss or permissions concern you could not reproduce.
  - `open`: not resolved yet. Nothing may stay `open` when the loop ends.
- `commit`: short SHA, for `fixed` and `removed`.
- `arbiter`: empty, `dispute-upheld` or `finding-upheld`.
- `reopens`: the id of an earlier fixed finding this one is the same as (the fix did not hold).
- `note`: one line.

No tabs or newlines inside a field. Keep `claim` and `note` to one sentence each.

## The PR section

`scripts/report` prints the "Break loop" section from these two files, between
`<!-- cstack-break:start -->` and `<!-- cstack-break:end -->`. Replace everything between the
markers when you update a PR, and never edit the table by hand. `scripts/report check` lists
anything that would make the section wrong, such as open findings or fixes without commits.
