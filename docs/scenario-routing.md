# 情境技能路由

> 歷史參考：部分舊 skill 已移除或改名，對應連結指向移除前的版本。現行可用清單請參考 [README](../README.md)。

不知道該用哪個 skill 時，先用這張表。這份檔案只做導覽；真正的流程細節請點進各 skill。

## 先釐清需求

| 使用技能 | 情境 |
| --- | --- |
| [`/ask-matt`](../skills/engineering/ask-matt/SKILL.md) | 不知道目前情境該用哪個 skill 或流程 |
| [`/setup-matt-pocock-skills`](../skills/engineering/setup-matt-pocock-skills/SKILL.md) | 第一次在 repo 使用這套 skills，需要設定 issue tracker、triage labels 和 domain docs |
| [`/grill-me`](../skills/productivity/grill-me/SKILL.md) | 非程式碼計畫或設計還很模糊，需要先被問清楚 |
| [`/grill-with-docs`](../skills/engineering/grill-with-docs/SKILL.md) | 程式碼相關計畫需要訪談，同時想建立領域模型、術語和 ADR |

## 規劃與實作

| 使用技能 | 情境 |
| --- | --- |
| [`/to-spec`](../skills/engineering/to-spec/SKILL.md) | 已有足夠對話 context，要整理成 spec |
| [`/to-tickets`](../skills/engineering/to-tickets/SKILL.md) | 要把計畫、spec 或對話拆成宣告 blocking edges 的 tracer-bullet tickets |
| [`/implement`](../skills/engineering/implement/SKILL.md) | 已有 spec 或 tickets，要照著實作 |
| [`/tdd`](../skills/engineering/tdd/SKILL.md) | 要 test-first 實作功能或修 bug |
| [`/prototype`](../skills/engineering/prototype/SKILL.md) | 需要快速原型來驗證狀態模型、邏輯或 UI 方向 |
| [`/wayfinder`](../skills/engineering/wayfinder/SKILL.md) | 想法龐大又模糊，一個 session 裝不下（greenfield 專案、巨大 feature build），要先在 issue tracker 上繪製調查 ticket 地圖，逐一解決直到路徑清楚 |

## Debug、Review 與維護

| 使用技能 | 情境 |
| --- | --- |
| [`/diagnosing-bugs`](../skills/engineering/diagnosing-bugs/SKILL.md) | Bug 很難追、出現例外、失敗或效能退化 |
| [`/resolving-merge-conflicts`](https://github.com/djpken/matt-skills/blob/153fc1b93de6584562765cdce299324e1ff9e661/skills/engineering/resolving-merge-conflicts/SKILL.md) | 卡在 git merge 或 rebase conflict |
| [`/triage`](../skills/engineering/triage/SKILL.md) | Issues 或外部 PRs 需要分類、驗證、追問與寫 agent-ready brief |
| [`/code-review`](../skills/engineering/code-review/SKILL.md) | 要 review branch、PR 或 WIP diff 是否符合 spec 與 repo standards |
| [`/git-guardrails-claude-code`](../skills/misc/git-guardrails-claude-code/SKILL.md) | 想為危險 git 指令（push、reset --hard、clean 等）加防護 |
| [`/setup-pre-commit`](../skills/misc/setup-pre-commit/SKILL.md) | 要設定 Husky、lint-staged、Prettier、type checking 和 tests |

## 架構與領域模型

| 使用技能 | 情境 |
| --- | --- |
| [`/improve-codebase-architecture`](../skills/engineering/improve-codebase-architecture/SKILL.md) | 程式碼庫變亂，需要找深化機會並產出視覺化架構報告 |
| [`/codebase-design`](../skills/engineering/codebase-design/SKILL.md) | 要設計或改善深層模組：小介面、乾淨接縫、可測試 |
| [`/domain-modeling`](../skills/engineering/domain-modeling/SKILL.md) | 要建立或精煉專案領域模型、共同語言和 ADR |

## 交接與學習

| 使用技能 | 情境 |
| --- | --- |
| [`/handoff`](../skills/productivity/handoff/SKILL.md) | 要把目前對話壓縮成另一個 agent 可接手的交接檔案 |
| [`/teach`](../skills/productivity/teach/SKILL.md) | 想在多個 sessions 中學習新技術或概念 |
| [`/writing-great-skills`](https://github.com/djpken/matt-skills/blob/6bcbcb09e2f1ed5fa20b4e890c732ecbb58c6b64/skills/productivity/writing-great-skills/SKILL.md) | 想寫或改善自己的 skill |

## 大型決策與寫作

| 使用技能 | 情境 |
| --- | --- |
| [`/decision-mapping`](https://github.com/djpken/matt-skills/blob/b38badf7091afc614dedffc03ea8c8ad2b643cb4/skills/in-progress/decision-mapping/SKILL.md) | 想把鬆散想法拆成可調查 tickets，並逐一推進 |
| [`/loop-me`](../skills/in-progress/loop-me/SKILL.md) | 想把生活或工作中的重複 loop 規格化成 workflows |
| [`/writing-fragments`](../skills/in-progress/writing-fragments/SKILL.md) | 腦中有一堆想法，想先挖出素材、不急著成文 |
| [`/writing-shape`](../skills/in-progress/writing-shape/SKILL.md) | 已有原始素材，想逐段塑形成文章 |
| [`/writing-beats`](../skills/in-progress/writing-beats/SKILL.md) | 想像敘事一樣，beat by beat 拼出文章 |
| [`/edit-article`](https://github.com/djpken/matt-skills/blob/f958fa17c1b62c3f7be38fc09512669acf6b64fc/skills/personal/edit-article/SKILL.md) | 已有草稿，想重組 sections、改善清晰度並收緊 prose |
| [`/obsidian-vault`](https://github.com/djpken/matt-skills/blob/f958fa17c1b62c3f7be38fc09512669acf6b64fc/skills/personal/obsidian-vault/SKILL.md) | 要管理個人 Obsidian vault notes、wikilinks 和 index notes |

## 雜項工具

| 使用技能 | 情境 |
| --- | --- |
| [`/migrate-to-shoehorn`](../skills/misc/migrate-to-shoehorn/SKILL.md) | 要把 TypeScript tests 中的 `as` type assertions 遷移到 @total-typescript/shoehorn |
| [`/scaffold-exercises`](../skills/misc/scaffold-exercises/SKILL.md) | 要建立章節、題目、解答和解說的練習目錄結構 |

## 已棄用

| 曾做什麼 | 已棄用 skill |
| --- | --- |
| 用平行 sub-agents 生成多種 API / interface 設計方案 | [`/design-an-interface`](https://github.com/djpken/matt-skills/blob/f958fa17c1b62c3f7be38fc09512669acf6b64fc/skills/deprecated/design-an-interface/SKILL.md) |
| 對話式 QA 回報 bug 並自動開 issue | [`/qa`](https://github.com/djpken/matt-skills/blob/f958fa17c1b62c3f7be38fc09512669acf6b64fc/skills/deprecated/qa/SKILL.md) |
| 訪談後產出 refactor 計畫並開 issue | [`/request-refactor-plan`](https://github.com/djpken/matt-skills/blob/f958fa17c1b62c3f7be38fc09512669acf6b64fc/skills/deprecated/request-refactor-plan/SKILL.md) |
| 從對話萃取 DDD 術語表，存入 `UBIQUITOUS_LANGUAGE.md` | [`/ubiquitous-language`](https://github.com/djpken/matt-skills/blob/f958fa17c1b62c3f7be38fc09512669acf6b64fc/skills/deprecated/ubiquitous-language/SKILL.md) |
