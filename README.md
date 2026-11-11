# cstack

Chris-Jay's agent skills for building features and fixing bugs. They work in Claude Code and
Codex, and use [T3 Code](https://github.com/pingdotgg/t3code) features (cross-provider
subagents, PR watching, browser and device previews) when they are available. One repo serves
every machine in the [fleet](https://github.com/CM9ine/fleet).

## How it works

```
cstack-build ─┐                         ┌─ full-stop (default) ─ PR ready for you to merge
              ├─▶ cstack-break (loop) ─▶┤
cstack-fix ───┘                         └─ full-send ("full send") ─ PR merged
```

1. **Goal.** The agent writes the goal, the problem and the smallest change it expects, then
   waits for your OK. The confirmed goal is frozen.
2. **Build or fix.**
   - `cstack-build`: learn the code, decide the data shape, build, prove it works in the app.
   - `cstack-fix`: reproduce, prove the cause, commit a failing test, fix, prove it.
3. **Break loop.**
   - **Each round:** fresh adversaries, one Claude and one Codex, get the identical brief and
     try to break the change. Every finding needs a repro.
   - **The developer** reruns each repro, then fixes the finding or disputes it.
   - **Disputes** go to an arbiter from the other model family.
   - **Ending:** it repeats until a fresh round finds nothing, with no round limit.
4. **PR.**
   - **Filing:** one PR, with every round's findings and stats in the description.
   - **Babysitting:** through CI and review bots. Post-PR changes get rechecked.
   - **Default:** it stops when the PR is ready to merge.
   - **"full send":** said at any point, it merges once the merge gate passes.

## Usage

```
cstack-build add a dark mode toggle to settings
cstack-fix users get logged out after 5 minutes
cstack-fix the export drops its last row, full send
cstack-break                       # just the loop, on the current branch
full stop                          # PR and babysit the current branch
```

## Skills

| Skill | Triggers on | What it does |
| --- | --- | --- |
| `cstack-build` | "cstack-build …" | Goal, build, prove, break loop, PR |
| `cstack-fix` | "cstack-fix …" | Goal, reproduce, cause, failing test, fix, break loop, PR |
| `cstack-break` | "cstack-break" | The adversarial loop, on any change |
| `full-stop` | "full stop" | File the PR, babysit it, stop when it is ready |
| `full-send` | "full send" | The same, then merge |
| `frontend-design` | building or restyling a UI | Anthropic's design guidance: a deliberate visual direction, not template defaults |

Models for the adversaries and the arbiter are set in
[`skills/cstack-break/config.md`](skills/cstack-break/config.md).

## Install

On each machine, clone it wherever you keep projects, then run `install` from that checkout.
Safe to rerun: it skips the clone when the checkout exists.

```sh
dir=~/cstack-t3        # or wherever your projects live, such as ~/GitHub/cstack-t3
[ -d "$dir" ] || gh repo clone CM9ine/cstack-t3 "$dir"
"$dir/bin/cstack" install
```

Keep the checkout on the internal disk, outside iCloud-synced folders (`~/Desktop`,
`~/Documents`) and away from external drives: agents lose their skills if its files go missing.
The links point at the checkout's exact path, so to move it later, run `cstack uninstall`
first, move it, then `install` from the new place.

`install` symlinks every skill into `~/.claude/skills/` (Claude Code) and `~/.agents/skills/`
(Codex), and `cstack` into `~/.local/bin/`. It never replaces a file it did not create.

| Command | What it does |
| --- | --- |
| `cstack update` | `git pull --ff-only`, then `install` |
| `cstack status` | the checkout, and what is linked where |
| `cstack upstream [skill]` | changes upstream since adapted material was copied |
| `cstack uninstall` | remove every link into this checkout |

Skills load when a session starts, so start a new thread after installing or updating.

## Requirements

`git`, an authenticated `gh`, `python3`, and both the `claude` and `codex` CLIs logged in. The
break loop needs one model from each family.

The skills read their own supporting files (briefs, config, the goal step). In T3 Code agents
have full access, so this just works. In a plain Claude Code session with default permissions,
Claude Code only reads inside the project, and checks both the skill link and the checkout it
points to. Allow both in `~/.claude/settings.json`:

```json
{ "permissions": { "additionalDirectories": ["~/.claude/skills", "~/cstack-t3"] } }
```

Use your checkout's path in place of `~/cstack-t3`. `cstack status` prints it.

## Credits

`full-stop`, `full-send` and parts of `cstack-break` are adapted from
[t3dotgg/tstack](https://github.com/t3dotgg/tstack) (MIT). See [`upstream.tsv`](upstream.tsv).
`frontend-design` is Anthropic's skill from
[anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official)
(Apache 2.0, see its `LICENSE.txt`); only the `description` is changed.
Ideas from [pstack](https://github.com/cursor/plugins/tree/main/pstack) (MIT).
