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
- **toolCallStyle** - advisory: `native` | `json` | `xml`.
- **toolHints** - per-tool notes that teach a model your exact tool names (keyed by the real tool name), for models that reach for another framework's names like `create_file` or `bash`. Look the names up in [TOOLS.md](./TOOLS.md).

Frontier models (Claude, GPT, Gemini) intentionally have **no profile** here and
run unchanged. Profiles target the models that benefit: weaker, local, or quirky.

## Profiles in this library

| Profile | Matches | Why |
|---|---|---|
| DeepSeek | `deepseek` | V3-era / R1 / local distills: low temp + explicit tool-name hints for reliable multi-tool calling. |
| DeepSeek V4 | `deepseek-v4` | Agent-grade (V4 / Flash / Pro); vendor params (temp 1.0, top_p 1.0), no tool cap (it drives the full catalogue). |
| Qwen | `qwen` | Qwen2.5 / Qwen3: strong native tool-caller; vendor non-thinking params. |
| Qwen 3.5 | `qwen3.5` | Vendor params moved to temp 1.0, top_p 0.95, top_k 20. |
| MiniMax | `minimax` | Agentic tool-caller (M2.7 / M3); vendor params (temp 1.0, top_p 0.95, top_k 40), no tool cap (it drives the full catalogue). |
| GLM | `glm` | Solid tool-caller (4.x / 5); GLM-5 agentic-eval params (temp 0.7, top_p 0.95). |
| Kimi | `kimi` | Over-deliberates; instant-mode params (temp 0.6, top_p 0.95) + hint to act decisively. |
| Mistral | `mistral` | Low vendor temp; good for structured output. |
| Devstral | `devstral` | Mistral's 24B coding/agent model (matched no profile before); low deterministic temp + a coding-agent hint (edit precisely, verify with tests). |
| Gemma | `gemma` | Lower temp + compact prompt for reliable tool use. |
| Llama | `llama` | Vendor params. |
| Phi | `phi` | Verbose tool-caller; low temp + compact prompt. |
| GPT-OSS | `gpt-oss` | Strong native tool-caller for its class; decisive turns. |
| Nemotron | `nemotron` | NVIDIA Nemotron-3 (ultra / nano); middle-ground sampling so it gets the same per-model tuning as every other family. |
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
