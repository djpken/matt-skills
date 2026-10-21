---
name: scaffold-exercises
description: "建立包含章節、問題、解答與說明檔案的練習目錄結構，並確保透過 lint 檢查。當使用者想要建立練習框架、建立練習存根或設定新的課程章節時使用。"
---

<a id="scaffold-exercises"></a>

# 建立練習框架

建立透過 `pnpm ai-hero-cli internal lint` 的練習目錄結構，然後使用 `git commit` 進行 commit。

<a id="directory-naming"></a>

## 目錄命名

- **章節**：位於 `exercises/` 內的 `XX-section-name/`（例如：`01-retrieval-skill-building`）
- **練習**：位於章節內的 `XX.YY-exercise-name/`（例如：`01.03-retrieval-with-bm25`）
- 章節編號 = `XX`，練習編號 = `XX.YY`
- 名稱使用 dash-case（小寫，連字號）

<a id="exercise-variants"></a>

## 練習變體

每個練習至少需要以下其中一個子資料夾：

- `problem/` - 包含 TODO 的學生工作區
- `solution/` - 參考實作
- `explainer/` - 概念性教材，不含 TODO

建立存根時，除非計畫另有規定，否則預設使用 `explainer/`。

<a id="required-files"></a>

## 必要檔案

每個子資料夾（`problem/`、`solution/`、`explainer/`）都需要一個 `readme.md`，該檔案必須：

- **不可為空**（必須有實際內容，即使只有一行標題也可以）
- 沒有損壞的連結

建立存根時，建立一個包含標題和描述的最小 readme：

```md
# Exercise Title

Description here
```

如果子資料夾包含程式碼，還需要一個 `main.ts`（超過 1 行）。但對於存根而言，僅包含 readme 的練習是可以接受的。

<a id="workflow"></a>

## 工作流程

1. **解析計畫** - 提取章節名稱、練習名稱和變體型別
2. **建立目錄** - 為每個路徑執行 `mkdir -p`
3. **建立存根 readme** - 為每個變體資料夾建立一個包含標題的 `readme.md`
4. **執行 lint** - 使用 `pnpm ai-hero-cli internal lint` 進行驗證
5. **修正任何錯誤** - 反覆執行直到 lint 透過

<a id="lint-rules-summary"></a>

## Lint 規則摘要

Linter（`pnpm ai-hero-cli internal lint`）會檢查：

- 每個練習都有子資料夾（`problem/`、`solution/`、`explainer/`）
- 至少存在 `problem/`、`explainer/` 或 `explainer.1/` 其中一個
- 主要子資料夾中存在 `readme.md` 且非空
- 沒有 `.gitkeep` 檔案
- 沒有 `speaker-notes.md` 檔案
- Readme 中沒有損壞的連結
- Readme 中沒有 `pnpm run exercise` 指令
- 除非僅包含 readme，否則每個子資料夾都需要 `main.ts`

<a id="movingrenaming-exercises"></a>

## 移動或重新命名練習

重新編號或移動練習時：

1. 使用 `git mv`（而非 `mv`）來重新命名目錄，以保留 git 歷史記錄
2. 更新數字字首以維持順序
3. 移動後重新執行 lint

範例：

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

<a id="example-stubbing-from-a-plan"></a>

## 範例：根據計畫建立存根

假設計畫如下：

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

建立：

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

然後建立 readme 存根：

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
