---
name: obsidian-vault
description: 在 Obsidian vault 中搜尋、建立、管理 notes、wikilinks 和 index notes。用於使用者想在 Obsidian 中尋找、建立或整理 notes 時。
---

# Obsidian Vault

## Vault location

`/mnt/d/Obsidian Vault/AI Research/`

大多數 notes 都在扁平 root level。

## Naming conventions

- **Index notes**：聚合相關主題（例如 `Ralph Wiggum Index.md`、`Skills Index.md`、`RAG Index.md`）
- 所有 note names 使用 **Title Case**
- 不用 folders organization；改用 links 和 index notes

## Linking

- 使用 Obsidian `[[wikilinks]]` 語法：`[[Note Title]]`
- Notes 在底部 link 到 dependencies/related notes
- Index notes 只列出 `[[wikilinks]]`

## Workflows

### Search for notes

```bash
# Search by filename
find "/mnt/d/Obsidian Vault/AI Research/" -name "*.md" | grep -i "keyword"

# Search by content
grep -rl "keyword" "/mnt/d/Obsidian Vault/AI Research/" --include="*.md"
```

或直接在 vault path 上使用 Grep/Glob tools。

### Create new note

1. Filename 使用 **Title Case**
2. 依 vault rules 寫 learning unit 內容
3. 在底部加入相關 notes 的 `[[wikilinks]]`
4. 如果是 numbered sequence 的一部分，使用 hierarchical numbering scheme

### Related notes

搜尋 `[[Note Title]]` 以找 backlinks：

```bash
grep -rl "\\[\\[Note Title\\]\\]" "/mnt/d/Obsidian Vault/AI Research/"
```

### Find index notes

```bash
find "/mnt/d/Obsidian Vault/AI Research/" -name "*Index*"
```
