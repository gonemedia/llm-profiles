# Changelog

## 0.2.1 - 2026-06-08

- Added the MiniMax profile (`minimax`, covers M2 / M2.7). Vendor sampling per the
  MiniMax-M2.7 model card (HuggingFace / NVIDIA): temperature 1.0, top_p 0.95,
  top_k 40. Capable native tool-caller, so no tool cap; carries the shared tool
  hints.

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
