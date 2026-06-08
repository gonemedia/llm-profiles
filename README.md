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
- **promptHint** - a short, per-model instruction prepended to the system prompt.
- **params** - sampling: `temperature`, `top_p`, `top_k`.
- **toolCallStyle** - advisory: `native` | `json` | `xml`.
- **toolHints** - per-tool notes that teach a model your exact tool names (keyed by the real tool name), for models that reach for another framework's names like `create_file` or `bash`. Look the names up in [TOOLS.md](./TOOLS.md).

Frontier models (Claude, GPT, Gemini) intentionally have **no profile** here and
run unchanged. Profiles target the models that benefit: weaker, local, or quirky.

## Profiles in this library

| Profile | Matches | Why |
|---|---|---|
| DeepSeek | `deepseek` | Capable but less reliable at multi-tool calling; cap + explicit hint. |
| Qwen | `qwen` | Strong native tool-caller; vendor sampling params. |
| GLM | `glm` | Solid tool-caller; vendor params. |
| Kimi | `kimi` | Over-deliberates; hint to act decisively. |
| Mistral | `mistral` | Low vendor temp; good for structured output. |
| Gemma | `gemma` | Lower temp + smaller tool set for reliable tool use. |
| Llama | `llama` | Vendor params. |
| Small model | `*b` (<= ~9B ids) | Compact prompt + small tool set for tiny models. |

## How Skales uses them

In Skales: **Settings → LLM Profiles** (its own card, next to AI Providers) or the
dedicated **/profiles** page (opt-in). The most specific match wins (longest
literal pattern, provider pinned); an imported profile
outranks a built-in at equal specificity, so you can override one. Import a
profile by **file**, by pasting **JSON**, or by **URL** (point it at a raw file
in this repo). `index.json` is a manifest so a client can list and fetch the
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
