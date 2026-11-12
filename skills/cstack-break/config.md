# cstack-break config

Edit this file to change models. Use the model ids that T3 Code's `orchestrator_capabilities`
lists, which are the same ids the `claude` and `codex` CLIs accept.

## Adversaries

Both run in every round, whoever is developing. The name is what the record and the PR show.

| Name | Provider | Model | Effort |
| --- | --- | --- | --- |
| Fable | Claude | `claude-fable-5-2` | high |
| Sol | Codex | `gpt-6.1-sol` | high |

## Arbiter

Always from a different family than the developer, so it does not share the developer's blind
spots. The developer is the model running the main thread.

| Developer's family | Arbiter | Provider | Model | Effort |
| --- | --- | --- | --- | --- |
| Claude | Sol | Codex | `gpt-6.1-sol` | xhigh |
| Codex | Fable | Claude | `claude-fable-5-2` | xhigh |
| Anything else | Fable | Claude | `claude-fable-5-2` | xhigh |

## Effort option names

T3 Code's `delegate_task` takes effort under the option id the model lists in
`orchestrator_capabilities`: `effort` for Claude models, `reasoningEffort` for Codex models.
The CLIs take `--effort <level>` (`claude`) and `-c model_reasoning_effort="<level>"` (`codex`).
