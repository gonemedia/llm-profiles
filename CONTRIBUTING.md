# Contributing a profile

Profiles are small JSON files in `profiles/`. To add or improve one:

1. Copy an existing profile in `profiles/` to `profiles/<your-id>.json` and edit
   it. Follow [SCHEMA.md](./SCHEMA.md). The `id` must equal the filename.
2. Keep it **vendor-grounded**: cite the source for sampling params in `notes`
   (the model card, the vendor docs, or a reproducible benchmark). Bias
   `temperature` lower than the vendor default only when the model is weak at
   tool calling, and say so.
3. Be **conservative**: only set `maxTools` / `compactionLevel` for models that
   genuinely struggle with a big tool set or prompt. Do not throttle a capable
   model, and do not add profiles for frontier models that already call tools
   well (they intentionally have none).
4. Add your profile to `index.json` (the manifest: `id`, `name`, `file`,
   `modelPattern`) and bump the `updated` date.
5. Validate: the file must be valid JSON and parse against the schema. Test it
   in Skales (Settings → AI Provider → LLM Profiles → import by file) against the
   target model before opening the PR.
6. Open a PR describing the model, the problem the profile solves, and how you
   verified it improves tool-calling or output quality.

## What gets merged

- Profiles that demonstrably improve reliability for a real, reachable model.
- Clear, reproducible reasoning for every param (no cargo-culting).

## What does not

- "Tune everything to temperature 0" blanket profiles.
- Profiles for frontier models that already work well.
- Unverified guesses, or params copied without a source.
