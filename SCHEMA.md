# Profile schema

A profile is a single JSON object. Only `id`, `name`, and `match.modelPattern`
are required; everything else is optional and omitted fields simply do not apply.

```jsonc
{
  "id": "deepseek",                    // unique slug, also the filename
  "name": "DeepSeek",                  // display name
  "match": {
    "provider": "openrouter",          // optional; when set, must also match
    "modelPattern": "deepseek"         // substring, or a glob with * (e.g. "qwen", "*-8b")
  },
  "params": {                          // optional sampling overrides
    "temperature": 0.3,
    "top_p": 0.95,
    "top_k": 40
  },
  "toolParams": {                      // optional: sampling on a turn that OFFERS tools
    "temperature": 0.2                 // when omitted, params.temperature is clamped to <=0.3 for tool turns
  },
  "maxTools": 16,                      // optional: cap the offered tool set
  "compactionLevel": 1,                // optional: 0 full, 1 compact, 2 minimal
  "promptHint": "Call one tool at a time.", // optional: prepended to the system prompt
  "toolCallStyle": "native",           // optional advisory: native | json | xml
  "toolHints": {                       // optional: per-tool notes in THIS model's terms
    "write_file": "Writes or creates a file. There is no create_file; use write_file.",
    "execute_command": "Runs one shell command, not for reading or writing files."
  },
  "notes": "Why this profile exists."  // optional, human-only; ignored at runtime
}
```

## Matching

- `modelPattern` is matched (case-insensitive) against the active model id: a
  plain substring, or a glob where `*` is a wildcard.
- The special pattern `*b` matches only ids that advertise a small parameter
  count (`<= ~9B`, e.g. `llama-3.1-8b`, `qwen2.5-7b`); it does not match `70b`.
- On multiple matches, the most specific wins: longest literal pattern, and a
  pinned `provider` adds specificity. An imported profile beats a built-in at a
  tie, so you can override a built-in by shipping the same pattern.

## Guidance

- Prefer the model vendor's recommended sampling params; bias `temperature`
  lower than the vendor default when the family is weak at tool calling.
- `params` applies to every call; `toolParams` overrides it only on a turn that
  offers tools, where deterministic sampling yields cleaner tool-call JSON. Leave
  `toolParams` out to accept the default: the base temperature is clamped down to
  0.3 for tool turns and restored for prose. This never removes a tool; it only
  shapes how reliably the call is emitted.
- Only set `maxTools` / `compactionLevel` for models that genuinely struggle
- Careful with `maxTools`: the cap keeps core tools by tier, but dynamically
  registered tools (MCP servers, `mcp_<server>_*`) rank low and are dropped
  first - a capped profile can silently disable connected MCP backends (e.g.
  media generation in Studio Flow). Prefer no cap unless the model truly
  chokes on large tool sets.
  with a large tool set or prompt; do not throttle a capable model.
- Keep `promptHint` short and lead with the most important guidance: it is a
  nudge, not a manual. Skales clamps the field on import (Skales 12.1.1 and
  earlier cut it at 600 characters; 12.2.0+ raises the bound to 1500 and warns
  in the log when a hint is truncated). Because released clients still enforce
  600, keep library hints at or under 600 characters until 12.2.0 has rolled
  out, so a synced hint is never cut mid-sentence on an older app.
- Use `toolHints` to fix a specific name mismatch: a map of Skales tool name ->
  a short explanation in the model's own terms. Set it when a model natively
  reaches for a different name (e.g. emits `create_file` when Skales' tool is
  `write_file`). Each value is one line; keys are real Skales tool names. This is
  the per-tool layer that sits on top of the general `promptHint`. Up to 40
  entries; oversized maps and non-string values are dropped on import.
- Keys must be exact Skales tool names. You do not have to know them by heart:
  the full catalogue is in [TOOLS.md](./TOOLS.md), and the in-app `/profiles`
  page lists them too. You only need hints for the few a model gets wrong.

## Safety

A profile is pure data, never code: Skales runs nothing from it. But two fields,
`promptHint` and `toolHints`, are injected into the model's system prompt, so a
profile from an untrusted author is a prompt-injection vector (it can try to steer
a tool-using agent). Treat importing a profile like running someone's config:

- Import only from sources you trust. Skales shows exactly what a profile adds to
  the prompt (expand "What it adds to the prompt" on the profile) so you can read
  it before relying on it.
- On import Skales bounds the rest: `id` is filename-sanitised (no path
  traversal), `params` and `toolParams` are clamped to sane ranges (temperature
  0-2, top_p 0-1, top_k 0-500), `compactionLevel` must be 0/1/2, `toolHints` is capped at 40
  entries, and a `modelPattern` with many wildcards is matched without a
  backtracking regex. URL imports go through an SSRF-guarded fetch with a size cap.
- Keep `promptHint`/`toolHints` to factual, on-topic guidance. Anything that reads
  like an instruction to exfiltrate data or run commands is a red flag.
