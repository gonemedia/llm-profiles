# LLM Profiles

Per-model tuning profiles that make AI agents call tools reliably across very
different models. Tool-call reliability and the right sampling parameters vary a
lot by model (first-attempt tool-call success ranges roughly Qwen ~94% > GLM
~91% > DeepSeek ~87%, and each family ships different recommended temperatures),
so one global setting can never fit them all. A profile matches a provider/model
and supplies the right knobs.

Built for [Skales](https://skales.app), usable as a plain data format anywhere.

## What a profile tunes

- **maxTools** - cap the number of tools offered (helps models that choke on a big tool set).
- **compactionLevel** - prompt compaction floor (0 full, 1 compact, 2 minimal).
- **promptHint** - an instruction prepended to the system prompt: per-model guidance (decisiveness, grounding) plus the library's shared voice rules (act in the same turn you announce a tool, answer like a colleague, no filler or fake knowledge-cutoff disclaimers, check context before asking).
- **params** - sampling: `temperature`, `top_p`, `top_k`.
- **toolCallStyle** - `native` | `json` | `xml`: what the model does with tool calls. `json`/`xml` says it writes them as text, which switches on the host's fenced-block recovery so such a call still runs instead of showing up as the answer.
- **toolHints** - per-tool notes that teach a model your exact tool names (keyed by the real tool name), for models that reach for another framework's names like `create_file` or `bash`. Look the names up in [TOOLS.md](./TOOLS.md).

Frontier models (Claude, GPT, Gemini) intentionally have **no profile** here and
run unchanged. Profiles target the models that benefit: weaker, local, or quirky.

## Profiles in this library

All 25 profiles, grouped by family (`index.json` keeps them in the order they were
added, which is the manifest's job, not a reading order). A profile with no sampling
params carries the behaviour layer only: one tool at a time, no repeated call,
exact tool names, say what a failed tool reported, and the system prompt outranks
a squad brief. That is deliberate - this repo does not ship numbers it cannot cite.

| Profile | Matches | Why |
|---|---|---|
| DeepSeek | `deepseek` | V3-era / R1 / local distills: low temp + explicit tool-name hints for reliable multi-tool calling. |
| DeepSeek V4 | `deepseek-v4` | Agent-grade (V4 / Flash / Pro); vendor params (temp 1.0, top_p 1.0), no tool cap (it drives the full catalogue). |
| Qwen | `qwen` | Qwen2.5 / Qwen3: strong native tool-caller; vendor non-thinking params (temp 0.7, top_p 0.8, top_k 20). Also where `qwen3-max` and `qwen3.7` land, and correctly so. |
| Qwen 3.5 | `qwen3.5` | Vendor params moved to temp 1.0, top_p 0.95, top_k 20. |
| Qwen 3.6 | `qwen3.6` | `qwen3.5` does not match 3.6, so it fell back to Qwen2.5-era params. Same vendor numbers as 3.5, plus the local dialect (a bare typed JSON object instead of a native call) named in the hint. |
| MiniMax | `minimax` | Agentic tool-caller (M2.7 / M3); vendor params (temp 1.0, top_p 0.95, top_k 40), no tool cap (it drives the full catalogue). |
| MiniMax abab | `abab` | The older MiniMax namespace, including `abab6.5s-chat`, the provider's recommended pick: no `abab` id contains `minimax`, so the default model ran with no profile. No params - the M2.7/M3 cards say nothing about abab. |
| GLM | `glm` | Solid tool-caller (4.x / 5); GLM-5 agentic-eval params (temp 0.7, top_p 0.95). |
| GigaChat | `gigachat` | Sber's family: names tools that are not offered (`web_search` for `search_web`) and sometimes writes the call as XML in its text. Exact-name `toolHints`, native calls only, deterministic tool turns (toolParams temp 0.2). |
| Kimi | `kimi` | Over-deliberates; instant-mode params + hint to act decisively. Note the endpoint pins its temperature and rejects ours, so only `top_p` lands. |
| Mistral | `mistral` | Low vendor temp; good for structured output. |
| Magistral | `magistral` | Mistral's reasoning line. Its ids contain `mistral`, so a long-chain model was being handed Mistral Small's 0.15; its card says top_p 0.95, temp 0.7, word for word. The longer literal now wins. |
| Devstral | `devstral` | Mistral's 24B coding/agent model (matched no profile before); low deterministic temp + a coding-agent hint (edit precisely, verify with tests). |
| Codestral | `codestral` | `codestral` contains no `mistral`, so the code model matched nothing. No params - Mistral publishes none for it, and inheriting the family's 0.15 is the guess this repo does not make. |
| Mixtral | `mixtral` | Spelled with an x, so a bare Ollama `mixtral` matched nothing while `mistralai/Mixtral-8x22B` matched through the vendor prefix. No params, same reason as Codestral. |
| Gemma | `gemma` | Lower temp + compact prompt for reliable tool use (Gemma 2 era). |
| Gemma 4 | `gemma4` | Measured, not inherited: Gemma 4 calls tools natively, and the card asks for temp 1.0, top_p 0.95, top_k 64 with a 131k window - the family profile's 0.3 and compaction floor were pure loss. |
| Gemma 4 (vendor id) | `gemma-4` | Google's own ids are hyphenated (`google/gemma-4-27b-it`), which `gemma4` does not match. A deliberate twin, not a variant. The pattern is a literal, because a glob would also swallow `gemma-3-4b-it`. |
| Hunyuan Hy3 | `hy3` | Tencent's flagship and the default Skales selects for the Hunyuan provider. Its ids contain no `hunyuan` substring at all. Sampling from the official vLLM recipe (temp 0.9, top_p 1.0). |
| Hunyuan (Tencent) | `hunyuan` | The other Hunyuan namespace on the legacy host (`hunyuan-turbos`, `-vision`, `-a13b`), which answers to neither `hy3` nor anything else. No params - the A13B card recommends none. |
| Llama | `llama` | Vendor params. |
| Nemotron | `nemotron` | NVIDIA Nemotron-3 (ultra / nano); middle-ground sampling, and `longThinking` so a long silent stretch is not read as a stalled stream. |
| Phi | `phi` | Verbose tool-caller; low temp + compact prompt. |
| GPT-OSS | `gpt-oss` | Strong native tool-caller for its class; decisive turns. |
| Small model | `*b` (<= ~9B ids) | Compact prompt for tiny models (tool set is trimmed by the host's local-model setting, not the profile). |

## How Skales uses them

In Skales: **Settings → LLM Profiles** (its own card, next to AI Providers) or the
dedicated **/profiles** page (opt-in). The most specific match wins (longest
literal pattern, provider pinned); an imported profile
outranks a built-in at equal specificity, so you can override one. Import a
profile by **file**, by pasting **JSON**, or by **URL** - point it at a raw file
in this repo (from Skales v11.3.2 a normal GitHub file link works too; it is
rewritten to the raw file automatically). `index.json` is a manifest so a client can list and fetch the
current set and stay up to date as profiles are added.

## Schema

See [SCHEMA.md](./SCHEMA.md). A profile is a small JSON file; the format is kept
deliberately simple so a profile is easy to write, review, and share.

## Safety

A profile is data, not code, but `promptHint` and `toolHints` are injected into
the model's system prompt, so an untrusted profile is a prompt-injection vector.
Import only from sources you trust; Skales shows exactly what a profile injects
before you rely on it, clamps params to sane ranges, and bounds the importer. See
the Safety section in [SCHEMA.md](./SCHEMA.md).

## Contributing

Profiles are community-extensible. Add or improve one via PR, see
[CONTRIBUTING.md](./CONTRIBUTING.md). Keep entries vendor-grounded (cite the
source for params) and conservative (do not over-tune a capable model).

## License

The profile data here is provided for use with Skales and compatible tools.
Skales is BSL-1.1. These profiles are configuration data, not Skales source.
