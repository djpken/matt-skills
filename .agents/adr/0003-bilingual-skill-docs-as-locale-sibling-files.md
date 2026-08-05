# Bilingual skill docs ship as locale-suffixed sibling files, never as edits to the English source

Skills and their reference docs are read by both agents and humans. A growing share of users read Traditional Chinese (zh-TW) as their working language, so the repo carries a parallel translation for the reader-facing prose.

## Decision

For every `SKILL.md`, every reference file it discloses via a context pointer, and the top-level `README.md`, a translation lives beside it as `<file>.zh.md` (e.g. `SKILL.zh.md`, `LOGIC.zh.md`, `README.zh.md`). The suffix is `.zh`, not `.zh-TW` — don't "fix" it to match the locale below, that would orphan every existing sibling. The register behind that suffix is standard written Traditional Chinese (zh-TW), excluding Cantonese colloquial markers (嘅/喺/唔/咗/呢個/嗰/佢哋).

- **The English file is the source of truth and is never edited to add a translation.** The `.zh.md` file is purely additive.
- **`name:` in `SKILL.md` frontmatter is copied byte-for-byte into `SKILL.zh.md`, never translated.** It's the invocation key — translating it would break `/`-invocation and cross-skill references.
- **`description:` is translated.** It's read by humans and the model when choosing a skill; the English key stays in `name:`.
- **Links inside a `.zh.md` file point at the English filename** (e.g. `[LOGIC.md](LOGIC.md)`, not `LOGIC.zh.md`), matching every existing translated sibling in this repo. A `.zh.md` reader who follows a link lands on the English file; there's no rewritten-pointer graph to keep in sync.
- **`CLAUDE.md`, `RELEASE-NOTES.md`, and the plugin manifests are never translated.** Plugin manifests (`.claude-plugin/plugin.json`, `marketplace.json`) point at skill *directories*, not individual files, so an added `.zh.md` sibling never needs a manifest update — this is what keeps the convention low-maintenance.
- Bucket-level `README.md`s (`skills/engineering/README.md`, `skills/productivity/README.md`, etc.) are **not** translated — only the top-level `README.md` carries a sibling. This matches the convention already in place before this ADR was written.

## Why sibling files, not inline translation or a locale folder

- **Never editing the English source** means an upstream merge can never produce a translation-shaped conflict in the file agents actually execute from.
- **A flat sibling (`SKILL.zh.md`) beside `SKILL.md`**, rather than a `zh/` mirror directory, keeps a skill's whole file set — English and translated — in one folder, so adding, renaming, or deleting a skill touches one location.

## Consequence

Whenever a `SKILL.md` or a file it points to changes in a way that shifts its meaning or structure (not just wording), the corresponding `.zh.md` sibling needs the same shift, not just a patch of the literal diff — translations drift structurally, not line-by-line, so re-syncing them is a re-read-and-rewrite of the affected sections, not a mechanical diff apply.
