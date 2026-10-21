> 狀態：已由 [0004-traditional-chinese-primary-documents.md](0004-traditional-chinese-primary-documents.md) 取代。以下保留先前的雙語對應檔決策供追溯。

# Bilingual skill documents use locale sibling files

This fork uses `zh-TW` as its default branch. Its existing `*.zh.md` siblings represent standard written Taiwan Traditional Chinese. Keep the `.zh` suffix so existing links and translations remain valid; do not rename it to `.zh-TW`.

The English files remain the source of truth. Translate every `SKILL.md`, its disclosed Markdown references, README files, and reader-facing skill guides into sibling files. Only `description` changes in skill frontmatter; preserve `name` and every other field. Preserve code blocks, inline code, commands, paths, URLs, and plugin manifests. Do not translate `AGENTS.md`, `CLAUDE.md`, `RELEASE-NOTES.md`, or changelogs.

The top-level English README may carry a Chinese navigation banner. Reference links inside translations retain English destinations for source comparison. The Chinese document index links directly to translated files, and translated headings retain anchors for the English headings.

Sibling files keep executable skill sources unchanged and reduce upstream merge conflicts. Sync a translation whenever its source changes; remove a translated skill entry when the upstream skill is removed. Translations are machine-assisted and require coverage, frontmatter, literal-preservation, and Taiwan written-register checks before publication. The English source governs when meanings differ.
