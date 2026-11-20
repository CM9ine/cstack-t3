# cstack-break config

Edit this file to change models. Use the model ids that T3 Code's `orchestrator_capabilities`
lists.

## Adversaries

All of them run in every round, whoever is developing. The name is what the record and the PR
show.

| Name | Provider | Model | Effort |
| --- | --- | --- | --- |
| Opus | Claude | `claude-opus-5-5` | high |
| Sol | Codex | `gpt-6.2-sol` | high |
| Neon | Antigravity | `gemini-4-neon` | high |

## Arbiter

Always from a different family than the developer, so it does not share the developer's blind
spots. The developer is the model running the main thread.

| Developer's family | Arbiter | Provider | Model | Effort |
| --- | --- | --- | --- | --- |
| Claude | Sol | Codex | `gpt-6.2-sol` | xhigh |
| Codex | Opus | Claude | `claude-opus-5-5` | xhigh |
| Anything else | Opus | Claude | `claude-opus-5-5` | xhigh |

## Effort option names

T3 Code's `delegate_task` takes effort under the option id the model lists in
`orchestrator_capabilities`: `effort` for Claude models, `reasoningEffort` for Codex models,
`thought_level` for Antigravity (Gemini) models.
