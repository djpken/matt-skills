# 情境技能路由

不知道該用哪個 skill 時，先用這張表。這份文件只做導覽；真正的流程細節請點進各 skill。

## 先釐清需求

| 使用技能 | 情境 |
| --- | --- |
| [`/ask-matt`](../skills/engineering/ask-matt/SKILL.zh.md) | 不知道目前情境該用哪個 skill 或流程 |
| [`/setup-matt-pocock-skills`](../skills/engineering/setup-matt-pocock-skills/SKILL.zh.md) | 第一次在 repo 使用這套 skills，需要設定 issue tracker、triage labels 和 domain docs |
| [`/grill-me`](../skills/productivity/grill-me/SKILL.zh.md) | 非程式碼計畫或設計還很模糊，需要先被問清楚 |
| [`/grill-with-docs`](../skills/engineering/grill-with-docs/SKILL.zh.md) | 程式碼相關計畫需要訪談，同時想建立領域模型、術語和 ADR |

## 規劃與實作

| 使用技能 | 情境 |
| --- | --- |
| [`/to-spec`](../skills/engineering/to-spec/SKILL.zh.md) | 已有足夠對話 context，要整理成 spec |
| [`/to-tickets`](../skills/engineering/to-tickets/SKILL.zh.md) | 要把計畫、spec 或對話拆成宣告 blocking edges 的 tracer-bullet tickets |
| [`/implement`](../skills/engineering/implement/SKILL.zh.md) | 已有 spec 或 tickets，要照著實作 |
| [`/tdd`](../skills/engineering/tdd/SKILL.zh.md) | 要 test-first 實作功能或修 bug |
| [`/prototype`](../skills/engineering/prototype/SKILL.zh.md) | 需要快速原型來驗證狀態模型、邏輯或 UI 方向 |
| [`/wayfinder`](../skills/engineering/wayfinder/SKILL.zh.md) | 想法龐大又模糊，一個 session 裝不下（greenfield 專案、巨大 feature build），要先在 issue tracker 上繪製調查 ticket 地圖，逐一解決直到路徑清楚 |

## Debug、Review 與維護

| 使用技能 | 情境 |
| --- | --- |
| [`/diagnosing-bugs`](../skills/engineering/diagnosing-bugs/SKILL.zh.md) | Bug 很難追、出現例外、失敗或效能退化 |
| [`/resolving-merge-conflicts`](../skills/engineering/resolving-merge-conflicts/SKILL.zh.md) | 卡在 git merge 或 rebase conflict |
| [`/triage`](../skills/engineering/triage/SKILL.zh.md) | Issues 或外部 PRs 需要分類、驗證、追問與寫 agent-ready brief |
| [`/code-review`](../skills/engineering/code-review/SKILL.zh.md) | 要 review branch、PR 或 WIP diff 是否符合 spec 與 repo standards |
| [`/git-guardrails-claude-code`](../skills/misc/git-guardrails-claude-code/SKILL.zh.md) | 想為危險 git 指令（push、reset --hard、clean 等）加防護 |
| [`/setup-pre-commit`](../skills/misc/setup-pre-commit/SKILL.zh.md) | 要設定 Husky、lint-staged、Prettier、type checking 和 tests |

## 架構與領域模型

| 使用技能 | 情境 |
| --- | --- |
| [`/improve-codebase-architecture`](../skills/engineering/improve-codebase-architecture/SKILL.zh.md) | 程式碼庫變亂，需要找深化機會並產出視覺化架構報告 |
| [`/codebase-design`](../skills/engineering/codebase-design/SKILL.zh.md) | 要設計或改善深層模組：小介面、乾淨接縫、可測試 |
| [`/domain-modeling`](../skills/engineering/domain-modeling/SKILL.zh.md) | 要建立或精煉專案領域模型、共同語言和 ADR |

## 交接與學習

| 使用技能 | 情境 |
| --- | --- |
| [`/handoff`](../skills/productivity/handoff/SKILL.zh.md) | 要把目前對話壓縮成另一個 agent 可接手的交接文件 |
| [`/teach`](../skills/productivity/teach/SKILL.zh.md) | 想在多個 sessions 中學習新技術或概念 |
| [`/writing-great-skills`](../skills/productivity/writing-great-skills/SKILL.zh.md) | 想寫或改善自己的 skill |

## 大型決策與寫作

| 使用技能 | 情境 |
| --- | --- |
| [`/decision-mapping`](../skills/in-progress/decision-mapping/SKILL.zh.md) | 想把鬆散想法拆成可調查 tickets，並逐一推進 |
| [`/loop-me`](../skills/in-progress/loop-me/SKILL.zh.md) | 想把生活或工作中的重複 loop 規格化成 workflows |
| [`/writing-fragments`](../skills/in-progress/writing-fragments/SKILL.zh.md) | 腦中有一堆想法，想先挖出素材、不急著成文 |
| [`/writing-shape`](../skills/in-progress/writing-shape/SKILL.zh.md) | 已有原始素材，想逐段塑形成文章 |
| [`/writing-beats`](../skills/in-progress/writing-beats/SKILL.zh.md) | 想像敘事一樣，beat by beat 拼出文章 |
| [`/edit-article`](../skills/personal/edit-article/SKILL.zh.md) | 已有草稿，想重組 sections、改善清晰度並收緊 prose |
| [`/obsidian-vault`](../skills/personal/obsidian-vault/SKILL.zh.md) | 要管理個人 Obsidian vault notes、wikilinks 和 index notes |

## 雜項工具

| 使用技能 | 情境 |
| --- | --- |
| [`/migrate-to-shoehorn`](../skills/misc/migrate-to-shoehorn/SKILL.zh.md) | 要把 TypeScript tests 中的 `as` type assertions 遷移到 @total-typescript/shoehorn |
| [`/scaffold-exercises`](../skills/misc/scaffold-exercises/SKILL.zh.md) | 要建立章節、題目、解答和解說的練習目錄結構 |

## 已棄用

| 曾做什麼 | 已棄用 skill |
| --- | --- |
| 用平行 sub-agents 生成多種 API / interface 設計方案 | [`/design-an-interface`](../skills/deprecated/design-an-interface/SKILL.zh.md) |
| 對話式 QA 回報 bug 並自動開 issue | [`/qa`](../skills/deprecated/qa/SKILL.zh.md) |
| 訪談後產出 refactor 計畫並開 issue | [`/request-refactor-plan`](../skills/deprecated/request-refactor-plan/SKILL.zh.md) |
| 從對話萃取 DDD 術語表，存入 `UBIQUITOUS_LANGUAGE.md` | [`/ubiquitous-language`](../skills/deprecated/ubiquitous-language/SKILL.zh.md) |
