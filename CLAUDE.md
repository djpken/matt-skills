Skills are organized into bucket folders under `skills/`:

- `engineering/` — daily code work
- `productivity/` — daily non-code workflow tools
- `misc/` — kept around but rarely used, not promoted
- `personal/` — tied to my own setup, not promoted
- `in-progress/` — drafts not yet ready to ship
- `deprecated/` — no longer used

Every skill in `engineering/` or `productivity/` (the **promoted** buckets) must have a reference in the top-level `README.md` and an entry in `.claude-plugin/plugin.json`. Skills in `misc/`, `personal/`, `in-progress/`, and `deprecated/` must not appear in either.

Each skill entry in the top-level `README.md` must link the skill name to its `SKILL.md`.

Each bucket folder has a `README.md` that lists every skill in the bucket with a one-line description, with the skill name linked to its `SKILL.md`. The promoted buckets' `README.md`s and the top-level `README.md` group entries into **User-invoked** and **Model-invoked**; non-promoted bucket `README.md`s (`misc/`, `personal/`) use a flat list.

Skills in `engineering/` and `productivity/` also have a human-facing docs page at `docs/<bucket>/<skill-name>.md` (the docs tree mirrors those two bucket folders under `skills/`). The published URL is `https://aihero.dev/skills-<skill-name>` regardless of bucket — the docs path is repo organisation only. When you add, rename, or change the behaviour of a skill in `engineering/` or `productivity/`, create or re-sync its docs page following [.agents/writing-docs.md](./.agents/writing-docs.md). Skills in the non-promoted buckets (`misc/`, `personal/`, `in-progress/`, `deprecated/`) get **no** docs page.

Every `SKILL.md` is either user-invoked (`disable-model-invocation: true`, reachable only by the human) or model-invoked (model- or user-reachable). See [.agents/invocation.md](./.agents/invocation.md).

[`ask-matt`](./skills/engineering/ask-matt/SKILL.md) is the router that maps every user-reachable skill and how they relate. The same trigger that re-syncs a docs page applies to it: whenever you add, rename, remove, or change how a user-reachable skill fits the flows, re-read `ask-matt`'s `SKILL.md` and update it so the map stays accurate — a new skill it never mentions, or a stale one it still routes to, is a router that lies.

To (re)link every skill into the local harness skill directories (`~/.claude/skills`, `~/.agents/skills`), run `scripts/link-skills.sh`. Each entry is a symlink into this repo, so a `git pull` keeps installed skills current; re-run the script after adding, removing, or renaming a skill.

## 同步上游更新（fork 專用）

這個 fork 額外維護繁體中文翻譯（每個 promoted / misc skill 的 `SKILL.zh.md`，以及根目錄的 `中文.md`）。每次「拉上游最新 code」時，照這個流程走：

1. `git fetch origin && git merge origin/main`（`origin` 指向上游 `mattpocock/skills`；`fork` 才是這個 repo 自己的 remote）。
2. 用 `git diff <合併前 main>..origin/main --name-status` 找出這次合併改了哪些 `SKILL.md` / `README.md` / `CLAUDE.md`。
3. 對每個內容有變動的 promoted 或 `misc/` skill：
   - 若原本就有 `SKILL.zh.md`，依照英文 diff 更新對應段落，保留既有翻譯用詞（例如 seam → 接縫、flow → 流程、tracer bullet → 曳光彈），front matter 的 key（`name`、`disable-model-invocation` 等）不譯，只譯 `description` 內容。
   - 若是新 skill 或從 `in-progress/`／`personal/` 移入 promoted 或 `misc/`，新增對應的 `SKILL.zh.md`。
   - 若 skill 被搬出 promoted / `misc/`（移到 `in-progress/`、`deprecated/` 等）或刪除，同步移除它的 `SKILL.zh.md`。
   - `in-progress/`、`personal/`、`deprecated/` 裡的 skill 不需要 `.zh.md`。
4. 同步根目錄 `中文.md` 的「技能總覽」區塊，讓它跟英文 `README.md` 的 Reference 區塊一致（User-invoked／Model-invoked 分類、skill 增刪與描述變動）；`README.md` 目前只列 `engineering/` 和 `productivity/`（misc 已不再列在頂層）。
5. 若 `docs/scenario-routing.md`（這個 fork 自己加的中文情境路由表，上游沒有）裡有指到被搬移或改名的 skill，順手修正連結。
6. 合併回 `main` 之後（不是在 worktree 裡）才執行 `scripts/link-skills.sh`，避免把本機 skill symlink 指到暫時性的 worktree 路徑。
