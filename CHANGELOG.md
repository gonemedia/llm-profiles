# Changelog

## 0.5.11 - 2026-09-01

One flag on four profiles, and the reason it could not be derived.

- **`longThinking` on DeepSeek V4, Qwen3.5, Qwen3.6 and MiniMax.** All four
  reason before they emit, and the silence in between is work. A host that has
  no flag to read measures that stretch against a short budget and cuts the
  stream while the model is still thinking, so the user sees a truncated answer
  and blames the model. The flag is the only way that fact travels.
- **Why no name rule can replace it.** For Qwen3.5 and Qwen3.6 thinking mode is
  a per-request choice rather than part of the model id, so nothing in the name
  says whether this call will pause. DeepSeek V4 and MiniMax M reason on every
  call regardless of how they are addressed. In both shapes the id is silent
  about the behaviour, which is exactly what a capability field is for.
- **Nothing else moved.** Sampling, tool-call style, tool hints and prompt
  hints are untouched; the notes on each profile now say why the flag is set.

## 0.5.10 - 2026-08-22

Two profiles added. Both come from the same check 0.5.7 ran: every model id the
app itself offers, put through the real resolver rather than reasoned about.
Two matched nothing.

- **Muse Glimmer gets a profile, and it is the one that mattered.** Meta's 30B
  open-weights agentic model (Apache-2.0, released this month) ships in Skales'
  own local catalogue described as the agentic flagship - and it matched no
  profile at all. Nothing could reach it: `muse-glimmer-30b-q4` contains no
  family name any pattern looks for, and the small-model catch-all stops at 9B,
  so the local agent the app recommends was the one running untuned. Sampling is
  the vendor's published run configuration, temperature 1.0, top_p 0.95,
  top_k 64, stated the same way on Meta's model card and on the Unsloth GGUF
  card Skales actually downloads from - two sources for one number, which is why
  this file is a citation and not an extrapolation. 131k context, so no
  compaction floor and no tool cap.
- **`longThinking` on it, `vision` deliberately not.** It reasons before it
  emits and its id contains none of the words a host's name rule looks for
  (`reasoner`, `thinking`, `o1`), which is the Nemotron and Qwen 3.7 case: a
  real silent stretch read as a stalled stream. Vision stays unset for the
  hunyuan-vision rule - the weights are multimodal, but locally the image path
  exists only when the separate mmproj companion is loaded, and the DFlash
  drafter that makes the model fast is documented as unusable with vision. One
  pattern covers all three states, and marking a possibly text-only run
  image-capable is worse than letting the host detect it.
- **The vendor's `Reasoning strength: low|medium|high|xhigh` line is left out.**
  The card recommends high or xhigh for agentic work, so it is citable, but it
  is also a real token-cost decision. A profile that raises what a run costs
  without being asked is not the behaviour layer's job.
- **DBRX gets the behaviour layer and no numbers.** `databricks/dbrx-instruct`
  is in the app's own Together list; the id carries no family substring and
  encodes no parameter count, so it reached neither a family profile nor the
  small-model catch-all and ran with no tool hints at all. That is the Mixtral
  case, and this is the same answer to it: exact tool names, one call at a time,
  say what a failed tool reported. No sampling params - the temperature 0.9 /
  top_p 0.9 pair that circulates for DBRX is the default of hosted playgrounds,
  not a vendor recommendation, and restating a server-side default is what this
  library declined to do for YandexGPT in 0.5.9.
- Checked and unchanged: the 2026 open-weight models the field actually talks
  about - MiniMax M3, Kimi K2.6/K2.7, GLM-5.x, DeepSeek V4, Qwen 3.6/3.7 - all
  already resolve to a family profile, so none of them needed one. Adding the
  two above reassigned nothing: every other id the app offers keeps the profile
  it had.
- `index.json` moves to 0.5.10 with the changelog: 28 profiles, 28 files, and
  the README table lists all of them.

## 0.5.9 - 2026-08-10

One profile added, and it is added because the mode changed, not because the
version number did.

- **Qwen 3.7 gets its own profile.** `qwen3.5` and `qwen3.6` do not match 3.7,
  so `qwen/qwen3.7-max` fell through to the generic `qwen` profile. That was the
  right call while nothing could be cited about 3.7, and it stopped being the
  right call once the vendor documentation said the model thinks on every turn:
  the generic profile's temperature 0.7 / top_p 0.8 / top_k 20 is the vendor's
  NON-thinking guidance, the wrong half of the card. The new file carries the
  vendor's own published 3.7 run configuration instead (temperature 1.0,
  top_p 0.95, top_k 20), which happens to be the same triple 3.5 and 3.6 use -
  cited, not inherited from its siblings.
- **`capabilities.longThinking` on that profile.** 3.7 reasons before it emits
  anything and its ids contain none of the words a host's name rule looks for
  (`reasoner`, `thinking`, `o1`), which is exactly the Nemotron case: a real
  silent stretch read as a stalled stream and cut mid-thought. The flag is the
  data answer to a question the name cannot answer.
- **`capabilities.vision` deliberately left out.** The default 3.7 id is
  text-only; a later dated snapshot added image input, and one pattern covers
  both. Marking a text model image-capable is worse than letting the host
  detect it, the same reasoning that keeps `hunyuan-vision` unflagged.
- YandexGPT was reviewed for a profile of its own and deliberately does not get
  one yet. The only number its API publishes is a server-side DEFAULT
  temperature, not a recommendation, and restating a default as a profile
  parameter is how a library starts shipping numbers it cannot cite. The family
  stays a note until an endpoint exists to measure it against.
- `index.json` moves to 0.5.9 with the changelog: 26 profiles, 26 files, the
  README table lists all of them.

## 0.5.8 - 2026-08-09

Documentation only: no profile changed, so nothing about how a model is driven
changed either. What changed is that three files stopped describing a library
that no longer exists.

- **`toolCallStyle` was documented as "optional advisory" while it had a
  consumer.** As of Skales 12.7.2 the field decides something: `json` or `xml`
  is the profile author's statement that this model writes tool calls as TEXT
  instead of using the native tool field, and that switches on the fenced-block
  recovery lane which is otherwise reserved for local and custom endpoints - so
  such a call still runs instead of leaking into the chat as the answer.
  `native`, and no profile at all, leave the lane off. "Advisory" told a
  contributor the field was decoration, which is the kind of note that survives
  for years and produces profiles that set it at random. SCHEMA.md now says what
  it does, including the reason the lane is not simply on for everyone: it costs
  a false-positive risk, because a fenced example a user asked for can read like
  a call.
- **The README profile table listed 15 profiles out of 25.** Ten families were
  missing from the one page that tells a reader what the library covers -
  GigaChat, Hunyuan (both namespaces), Magistral, Codestral, Mixtral, abab,
  Qwen 3.6 and both Gemma 4 spellings, i.e. everything added in 0.5.3 through
  0.5.7. The table is complete now, grouped by family rather than by the order
  entries were appended, and it states outright that a profile without sampling
  params is carrying the behaviour layer on purpose - that is a decision this
  library keeps making, not an unfinished file.
- **CONTRIBUTING.md sent contributors to a settings path that moved, and never
  mentioned the four lines a profile is not finished without.** LLM Profiles has
  its own card in Skales' settings (and a /profiles page); it has not been a
  sub-item of the AI Provider card for some time. The behavioural lines were
  written down in SCHEMA.md in 0.5.7 but not in the file someone actually follows
  step by step when writing their first profile. Added there too, with the 1500
  character bound and a step on checking a new pattern against ids that are not
  yours - anchored matching, specificity by literal characters, and the two
  collisions this library has already paid for (`magistral` inside `mistral`,
  `gemma-4` not matching `gemma4`).
- `index.json` moves to 0.5.8 with the changelog. Nothing else in it changed:
  same 25 profiles, same patterns, same params.

## 0.5.7 - 2026-08-09

- **The honesty line is now on every profile, not just the one it was written
  for.** 0.5.6 added it to GigaChat because that model answered a failed tool
  call by inventing plausible internal architecture instead of relaying the
  error. Nothing about that failure is a GigaChat trait: a model that cannot
  see why a tool failed will narrate something, and the profile layer was
  telling seventeen other families nothing about it. Three of them (Gemma,
  Phi, small models) already forbade claiming a file changed or a test passed
  without a tool result, which is the other half of the same rule and not a
  substitute - one bans inventing success, the other requires reporting the
  failure. All eighteen now carry both halves.
- **Seven families that Skales itself offers matched no profile at all.** Not
  guessed at: every id below is in the app's own curated model list, and each
  was run through the real resolver rather than reasoned about.
  - `hy3` and `hy3-preview`, the flagship Skales selects by default for the
    Hunyuan provider, contain no `hunyuan` substring, so no family profile
    could ever have reached them. Sampling from the official vLLM recipe for
    tencent/Hy3-preview: temperature 0.9, top_p 1.0, 256K context, hy_v3 tool
    parser.
  - `hunyuan-turbos-latest`, `hunyuan-vision` and `hunyuan-a13b` are the other
    Hunyuan namespace, served by the legacy host, and they answer to neither
    `hy3` nor anything else. The A13B card documents the window and native
    tool calling but recommends no sampling values, so this profile ships none.
  - `magistral` was the reverse problem: an id like mistralai/magistral-medium
    contains `mistral`, so a long-chain reasoning model was being handed the
    Mistral Small setting of temperature 0.15. Its card says the opposite,
    word for word: top_p 0.95, temperature 0.7. The longer literal now wins.
  - `codestral-latest` and a bare `mixtral` matched nothing, for the reason
    Devstral once did not: neither string contains `mistral`. Mixtral is the
    sharper case, because the same weights DO match through a vendor prefix
    (mistralai/Mixtral-8x22B), so the bare Ollama id was the only one running
    untuned. Mistral publishes no per-model sampling for either, so both carry
    the behaviour layer and no numbers.
  - `abab6.5s-chat` is the model Skales recommends on the MiniMax provider,
    and none of the abab ids contain `minimax`. The provider's default pick
    was the one running without a profile while M2.7 and M3 got the tuned one.
  - `gemma-4-27b-it` is Gemma 4 under Google's own hyphenated id, and 0.5.4
    fixed only the Ollama spelling: `gemma4` does not match `gemma-4`, so the
    vendor ids kept falling through to Gemma-2-era sampling and a compaction
    floor. A twin profile, not a variant - same model, same card. The pattern
    is the literal `gemma-4`, because a glob like `*gemma*4*` would also
    swallow google/gemma-3-4b-it.
- **`index.json` claimed libraryVersion 0.5.2 while the changelog stood at
  0.5.6.** Four releases of drift in the field that tells an installation what
  it is running. Now 0.5.7 and moving with the changelog.
- **A profile note described a tool cap this library removed in 0.3.3.** The
  generic DeepSeek notes still explained a raise from 16 to 24, and the Gemma
  notes still recommended a smaller tool set, on files that carry no `maxTools`
  at all. Notes are read by humans deciding what to copy, so a stale one is a
  wrong instruction with a long half-life. Both rewritten, and the Kimi notes
  now say plainly that its temperature never reaches the endpoint.
- **SCHEMA.md had a sentence cut in half by a later insertion** ("Only set
  `maxTools` / `compactionLevel` for models that genuinely struggle" ... "with
  a large tool set or prompt"), and it still told contributors to keep hints
  under 600 characters until Skales 12.2.0 rolled out. 12.2.0 shipped months
  ago, the bound is 1500, and every hint in the library has been over 600 for
  some time. Both fixed, and the four behavioural lines every profile must
  carry are now written down instead of being folklore.

## 0.5.6 - 2026-08-09

- **GigaChat gets a profile, written from a real-key report.** On some turns
  the model skips the native tool_calls field and writes the call into its
  text as Anthropic-style XML - and it names tools that were never offered
  (`web_search` for `search_web`), so the recovered call is dropped by design
  and the raw markup becomes the visible answer (skalesapp/skales#256, three
  failure shapes on GigaChat-3-Ultra, Gemini identical setup unaffected). The
  profile hammers exact tool names per hint and per prompt, demands the native
  calling mechanism, and adds an honesty line for the third finding: under a
  strong persona the model answered a failed tool with invented architecture
  instead of the error. A mitigation, said so in the notes - the host-side
  near-miss name resolution is a Skales fix, not a profile.

## 0.5.5 - 2026-08-06

- **Nemotron is declared a long silent thinker.** A user reported a
  nemotron-3-ultra turn arriving as a finished answer when it had actually
  been cut off. The cause was upstream of any profile: Skales decides how
  long a model may stay silent by looking at its NAME, and
  `nvidia/nemotron-3-ultra` contains none of the words that rule looks for,
  so it ran on the budgets of a fast model. `capabilities.longThinking` is
  the field that lets a profile answer that question with data instead, and
  this profile sets it. The next model of this class is a profile push.
- `capabilities.fixedTemperature` is documented in SCHEMA.md as well. It has
  been read by Skales since the Kimi endpoints started rejecting the field,
  but it was never written down here, and until 12.7.0 an imported profile
  could not actually carry it.

## 0.5.4 - 2026-08-06

- **Gemma 4 gets a profile of its own, written from a measurement.** The
  report behind it said Gemma refuses every tool on Ollama, so the model
  was pulled and asked: gemma4 emits native `tool_calls` in every
  combination tried (five tools and fifteen, vendor sampling and the
  generic Gemma profile's). The family assumption does not hold for this
  release, and nothing in the profile pushes it towards writing calls as
  text. What IS wrong is the same shape as the Qwen 3.5 / 3.6 split:
  `gemma` matches gemma4 by substring and hands it Gemma-2-era sampling
  (temperature 0.3) plus a compaction floor, while the model card asks for
  temperature 1.0, top_p 0.95, top_k 64 and the model carries a 131k
  window. Vision is declared, so the capability flag says so.
- Given no tools channel at all, gemma4 falls back to a Python-style call
  (`planner_create_task(title="...", due="...")`). Skales recovers that
  shape at parse time now, as a safety net for endpoints that drop the
  field; the profile is what keeps it from being needed.

## 0.5.3 - 2026-08-05

- **Qwen 3.6 gets a profile of its own.** Two reasons. The pattern:
  `qwen3.5` does not match qwen3.6, so the model fell through to the
  generic Qwen profile and ran on Qwen2.5-era sampling. The dialect:
  local builds (`qwen3.6-local` on Ollama) write tool calls as a bare
  typed JSON object (`{"type":"shell","command":"..."}`) instead of
  native tool_calls, so the user saw the raw object as the answer
  (skalesapp/skales dashboard report, 2026-08-05). The promptHint names
  that exact shape and the real tool behind it; Skales additionally
  recovers the shape at parse time, the profile stops it at the source.

## 0.5.2 - 2026-08-03

- **Squad discipline on every profile.** In multi-agent (team/squad) runs,
  local models were letting the coordinator's task brief override the rules
  and output contract in their own system prompt (skalesapp/skales#237,
  reproduced twice). Every promptHint now ends with the precedence rule:
  the brief says WHAT, the system prompt says HOW, and on conflict the
  system prompt wins, format contract included.

## 0.5.1 - 2026-08-03

- **Qwen: text-channel tool calls forbidden explicitly.** A user's qwen3:14b
  (custom runtime) emitted `<<TOOL_CALLS>> ... <<END_TOOL_CALLS>>` as plain
  text, so the call never ran and the raw markup leaked into the visible
  answer (and onward into a sent email). The Qwen promptHint now leads with:
  native tool-call channel only, never text, naming the observed dialects.

## 0.5.0 - 2026-07-29

- **A coding layer on every profile.** All 15 profiles now carry per-tool
  guidance for a coding session, not just for a chat. The rules a coding loop
  actually needs, in the tools that enforce them: read a file before changing
  it (`read_file`), change it with an exact-string `edit_file` rather than
  rewriting the whole thing (`edit_file`, `write_file`), run the check and
  report what it printed rather than claiming it passed (`test_run`), keep the
  checklist and continue from the next unchecked item (`update_todos`), read
  your own diff before calling a task done (`git_diff`).
- **Long-running commands.** The `execute_command` hint now points anything
  that does not end by itself - a dev server, a watcher, a long build - at
  `run_in_background` plus `read_shell_output`, phrased conditionally ("if it
  offers run_in_background") so an older Skales without those still reads a
  true sentence.
- **Per family, what that family gets wrong in a coding loop:**
  - **nemotron:** never re-emit a whole file for a two-line change; prefer the
    real tools over shelling out to cat/sed/git. Prompt hint gains the
    read-edit-run line.
  - **small, phi, gemma:** do not rewrite a file you have not read in full, and
    never write "tests pass" unless the tool printed it. gemma additionally:
    emit the tool call, do not print the edit as a code block and stop.
  - **glm, kimi:** decide the next step and take it - a long task is finished by
    acting on one item at a time, not by planning all of them (the
    reasoning-exhaustion failure).
  - **deepseek, deepseek-v4:** send the edit as a real tool call, never as
    tool-markup inside the answer text (the DSML leak).
- Prompt hints stay inside the 600-character budget that released clients
  enforce; where a family's extra line would have crossed it, the line went to
  the tool hints instead.

## 0.4.9 - 2026-07-15

- **GLM: on-demand tool loading unblocked.** The GLM profile gains a
  `load_tools` tool hint and a prompt-hint line: unlock extra tool groups by
  CALLING load_tools, never by narrating it in text. Pairs with the Skales
  12.4.0 harness change that lets GLM 5.x use on-demand tool retrieval again
  (the narrate-instead-of-call behavior was observed on the GLM-4 generation).


## 0.4.8 - 2026-07-12

- **Tool hints aligned with the Skales 12.3.0 harness.** All 15 profiles:
  `execute_command` now points multi-line or quote-heavy code to the new
  `run_script` tool (writes the code to a file and runs it with the right
  interpreter), and a `run_script` hint was added. `edit_file` gained a
  one-line recovery instruction for the new read-before-edit gate (read the
  file, retry the edit once). New `browser_read_page` hint teaches the
  structured browser flow: read the page as a ref-tagged text tree, click by
  ref id, re-read after navigation; screenshots only for image-heavy pages.
- **devstral:** promptHint now says "read_file first, then edit_file with
  exact old text", matching the read-before-edit gate.
- **TOOLS.md:** browser section rewritten for the 12.3.0 structured flow
  (browser_read_page, ref-based clicks, browser_login); run_script added.

## 0.4.7 - 2026-07-06

- **New optional `capabilities.vision` field.** A profile can now override
  image-capability detection for the matched model: `true` marks it
  image-capable, `false` text-only. Skales 12.2.0+ reads it (a local Ollama
  daemon's own capabilities array still wins first, then this override, then the
  built-in name list); older clients ignore it. Documented in SCHEMA.md.

## 0.4.6 - 2026-07-05

- **Anti-loop guidance in every profile, and every hint now fits the clamp.**
  A live run showed the expensive failure mode of weaker agents: a simple
  knowledge question escalated into multi-page fetches, repeated identical
  shell commands over cached files, and an aborted run with no visible answer.
  Every `promptHint` now leads with four rules against exactly that: answer
  stable knowledge directly (at most one search to verify current facts), never
  repeat a tool call with identical arguments, stop and write the answer once
  the material covers the question (never end a turn with only tool calls), and
  read pages with `extract_web_text` instead of grepping fetched files.
- **Hints trimmed to under 600 characters.** Skales clamps `promptHint` to 600
  characters on import; every profile's hint had grown past that, so the tail
  (including the shared voice guidance) was silently cut mid-sentence for
  synced users. Each hint was rewritten to fit, keeping the per-model essence
  (DeepSeek V4 chunked writes, Kimi decisiveness, GLM working-state trust,
  Devstral edit-and-verify, one-tool-at-a-time for small models). Documented in
  SCHEMA.md.

## 0.4.5 - 2026-07-02

- **Authoring turns keep full creative sampling.** Skales v12.1.0 marks Flow
  design turns (the artifact is written inside the tool call) as authoring
  turns: the deterministic tool-turn temperature clamp no longer applies there,
  so models with hot vendor guidance (DeepSeek V4, MiniMax at temperature 1.0)
  design at full creativity while ordinary tool calls stay clamped for JSON
  reliability. No profile changes needed; the host decides per turn.
- **Tool reference updated.** `extract_zip` (native archive extraction) added;
  `create_document` description corrected (Document panel, no approval).
- **Schema guidance:** warning added that `maxTools` drops MCP server tools
  first and can silently disable connected media backends.

## 0.4.4 - 2026-06-21

- **Every profile now carries shared voice guidance.** On top of the per-model
  tuning, each profile tells the model to answer like a colleague: no filler or
  thanks-for-reaching-out, own a mistake in one line instead of apologizing in a
  loop, never cite a training cutoff or claim it lacks real-time data (use
  search_web instead), and check the context before asking a question. These
  correct common weak-model habits. Frontier models match no profile and are
  unaffected. Sampling, tool hints and notes are unchanged.

## 0.4.3 - 2026-06-21

- **The same-turn rule now covers MiniMax, Qwen and Qwen 3.5.** The act-in-the-
  same-turn line added for the reasoning families in 0.4.2 is extended to
  MiniMax and the two Qwen profiles, so a model that announces a tool runs it
  in that message instead of narrating and stopping. Qwen and Qwen 3.5 gain a
  promptHint for the first time; their sampling, tool hints and notes are
  unchanged.

## 0.4.2 - 2026-06-21

- **Reasoning families act in the same turn they announce a tool.** Kimi, DeepSeek
  and GLM now carry an explicit line in their promptHint: when the model says it is
  about to use a tool or take an action, it must make that tool call in the same
  message instead of posting a "let me..." or "I will now..." line and stopping.
  Thinking models, Kimi in particular, would open a turn by narrating the action
  and end there without the call, so a tool-using request appeared to stall until
  the user nudged it. The sampling, tool hints and notes are unchanged.

## 0.4.1 - 2026-06-20

- **GLM gained a promptHint and a GLM-5.2 note.** The GLM profile carried good
  sampling and tool hints but no promptHint, so on a long agentic run GLM got
  none of the anti-giveup, tool-discovery and scheduling guidance the other
  families get. It now nudges the model to look a tool up with check_capabilities
  rather than invent a name or give up, to reach for planner_create_task /
  schedule_recurring_task for reminders, and on a long multi-step task to keep
  update_todos current and continue from the next unchecked item instead of
  starting over. The note now records GLM-5.2 (June 2026): ~1M context, up to
  131K output, dual reasoning, coding- and agent-first; sampling is unchanged
  (temperature 0.7, top_p 0.95, the vendor agentic setting).

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
