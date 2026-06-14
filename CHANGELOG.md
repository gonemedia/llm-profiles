# Changelog

## 0.4.0 - 2026-06-14

- **Devstral now has its own profile.** Mistral's Devstral-Small-2 (the 24B coding
  model) is a heavily used pick across OpenRouter, custom endpoints and Ollama, yet
  it matched no profile before: the id `devstral` does not contain `mistral`, so it
  fell through with zero tuning. It now binds a coding-agent profile with a low,
  deterministic temperature and a hint to edit precisely with `edit_file` and verify
  with `test_run` before reporting a task done.

- **Nemotron gained a profile.** NVIDIA's Nemotron-3 (ultra / nano) matched nothing
  before; it now gets middle-ground sampling like every other family, so a user on
  Nemotron gets the same per-model tuning instead of provider defaults.

- **New optional `toolParams`.** A profile can now set sampling that applies only on
  a turn that offers tools, where deterministic sampling produces cleaner tool-call
  JSON, while keeping the family's full `params` for prose. When `toolParams` is
  omitted the host clamps the base temperature down to 0.3 for tool turns and
  restores it afterward. This never removes a tool; it only shapes how reliably the
  call is emitted. See [SCHEMA.md](./SCHEMA.md).

## 0.3.4 - 2026-06-14

- **MiniMax gained a grounding hint for long Code-mode turns.** The family runs
  hot (vendor temperature 1.0) and, on a long folder-bound coding turn, could
  confabulate: swap the bound working folder for a half-remembered path and
  apologize in a loop instead of re-running the tool. The profile now tells it to
  trust the bound folder and the WORKING STATE exactly as written, to re-run the
  actual tool after a correction instead of repeating an apology, and to state a
  path, a file's contents or a git status only after a tool call confirms it.
  Sampling is unchanged (still the vendor card) and the full tool catalogue stays.

## 0.3.3 - 2026-06-13

- **Removed all per-profile tool caps.** A profile is a HELP layer - sampling,
  tool-name hints, a discovery hint - not a place to cut the tool set. A cap here
  matched models by NAME, so the official DeepSeek API names (`deepseek-chat` /
  `deepseek-reasoner`, which are V4-class now) and large variants (Mistral Large,
  Gemma 27B) were wrongly starved. Trimming the set for a genuinely small model is
  the host's job - its local-model tool budget plus size-based detection, where the
  essential tools (files, web, email, calendar, reminders, schedule) are always
  kept and the user controls the number. Strong models get the full catalogue.

## 0.3.2 - 2026-06-13

- **Reverted the MiniMax / DeepSeek V4 tool cap from 0.3.1.** Both are built to
  drive the full catalogue; capping them dropped tools the host prompt actively
  routes to them (a reminder needs `planner_create_task`, which fell outside the
  cap), so the model reached for the wrong tool and could not create a reminder.
  The tool-surface concern belongs to the host (force-included primaries + on-
  demand capability lookup), not to starving a strong model. Local/weak profiles
  keep their caps - they genuinely benefit from a smaller set.
- **Every profile gained a tool-discovery hint.** If a model is unsure whether a
  tool exists or which one fits, it now looks it up (`check_capabilities` /
  `fetch_skales_docs`) and uses the exact name, instead of inventing a name or
  telling the user it cannot be done. The biggest failure on differently-trained
  models was giving up while the right tool was one lookup away.

## 0.3.1 - 2026-06-13

- **MiniMax** and **DeepSeek V4**: added `maxTools: 40`. Both ran with no cap, so
  they were offered the host's full ~150-tool surface. In practice that large a
  surface hurts tool-call reliability (fixation), and on endpoints that do not
  surface native `tool_calls` the model is likelier to emit raw tool-call markup
  as text (the leak then shows up in the chat). 40 is a generous cap, well above
  the force-included floor (file core + lazy detail + configured integrations +
  MCP reserve + Tier 0), so nothing advertised is starved and on-demand
  capability lookup still reaches the rest. Tune up if you run many integrations.

## 0.2.1 - 2026-06-08

- Added the MiniMax profile (`minimax`, covers M2 / M2.7). Vendor sampling per the
  MiniMax-M2.7 model card (HuggingFace / NVIDIA): temperature 1.0, top_p 0.95,
  top_k 40. Capable native tool-caller, so no tool cap; carries the shared tool
  hints.

## 0.3.0 - 2026-06-11

Model refresh (researched against the current vendor model cards) and a wider
toolHints set, prompted by real-world reports that some families "circle" while
hunting for a tool that another family finds instantly.

- New profile **DeepSeek V4** (`deepseek-v4`, outranks the generic `deepseek`):
  vendor params temperature 1.0 / top_p 1.0 (official HF model cards, all
  modes) and **no tool cap**. The generic profile's tight cap starved V4-class
  models of most of the host's tools - the main reason they underperformed
  against MiniMax in agent use.
- New profile **Qwen 3.5** (`qwen3.5`, outranks `qwen`): vendor params
  temperature 1.0 / top_p 0.95 / top_k 20.
- DeepSeek (generic): maxTools 16 -> 24. The force-included core (files, web,
  configured integrations, MCP reserve) ate half of 16, leaving almost nothing
  for the priority tiers.
- MiniMax: notes updated for M2.7 / M3 (params unchanged - the M3 cards keep
  temperature 1.0 / top_p 0.95).
- GLM: temperature 0.6 -> 0.7 per GLM-5's own agentic evals (SWE-bench setting,
  HF model card).
- Kimi: top_p 0.95 added (vendor); temperature stays at the 0.6 instant-mode
  value because an agent loop wants decisive single steps.
- toolHints everywhere: +8 entries covering inbox reading (list_emails - the
  "read_emails / check_email / fetch_emails" trap), mark_email_read, calendar
  (list/create event), generate_image, ask_user, search_sessions, and
  update_todos. Models that reach for another framework's names now get steered
  to the real ones instead of circling.

## 0.2.0 - 2026-06-08

- Added `toolHints`: per-tool notes that teach a model your exact tool names
  (keyed by the real tool name), for models that reach for another framework's
  names (create_file, str_replace, bash, ls, ...). All eight built-in profiles
  now carry the same grounded 12-entry set covering the common file/shell/web
  tools.
- Added [TOOLS.md](./TOOLS.md): the full Skales tool-name catalogue, so toolHints
  keys can be looked up instead of guessed.
- SCHEMA.md: documented `toolHints` and added a Safety section. A profile injects
  text into the system prompt, so import only from trusted sources; on import
  params are clamped, oversized/ReDoS-prone patterns are bounded, and the fetch
  is size-capped.

## 0.1.0 - 2026-06-08

Initial seed, extracted from the Skales built-in profiles.

- Added profiles: DeepSeek, Qwen, GLM, Kimi, Mistral, Gemma, Llama, and a
  small-model catch-all (`*b`, <= ~9B ids).
- Added `index.json` manifest for fetch-from-repo so clients can list and pull
  the current set.
- Added SCHEMA.md and CONTRIBUTING.md.
