---
name: request-refactor-plan
description: 透過使用者訪談建立詳細 refactor plan，拆成微小 commits，然後建成 GitHub issue。用於使用者想規劃 refactor、建立 refactoring RFC，或把 refactor 拆成安全小步驟時。
---

當使用者想建立 refactor request 時，會呼叫這個 skill。依下列步驟進行。若你認為某些步驟不必要，可以跳過。

1. 請使用者詳細描述他們想解決的問題，以及任何可能的解法想法。

2. 探索 repo，驗證他們的說法並理解 codebase 目前狀態。

3. 詢問他們是否考慮過其他 options，並向他們呈現其他 options。

4. 針對 implementation 訪談使用者。要極度詳細且徹底。

5. 錘定 implementation 的精確 scope。弄清楚計畫改什麼，以及不改什麼。

6. 查看 codebase，確認這個區域的 test coverage。如果 test coverage 不足，詢問使用者對測試的計畫。

7. 將 implementation 拆成微小 commits 的計畫。記住 Martin Fowler 的建議：「make each refactoring step as small as possible, so that you can always see the program working.」

8. 用 refactor plan 建立 GitHub issue。Issue description 使用以下 template：

<refactor-plan-template>

## Problem Statement

開發者面對的問題，從開發者角度描述。

## Solution

問題的解法，從開發者角度描述。

## Commits

一份很長、很詳細的 implementation plan。用 plain English 撰寫，將 implementation 拆成盡可能小的 commits。每個 commit 都應讓 codebase 保持可運作。

## Decision Document

已做出的 implementation decisions 清單。可包含：

- 將建立/修改的 modules
- 將修改的 module interfaces
- 來自 developer 的技術釐清
- 架構決策
- Schema changes
- API contracts
- Specific interactions

不要包含特定 file paths 或 code snippets。它們可能很快過期。

## Testing Decisions

已做出的 testing decisions 清單。包含：

- 什麼構成好測試的描述（只測 external behavior，不測 implementation details）
- 哪些 modules 將被測試
- 測試的 prior art（也就是 codebase 中類似測試）

## Out of Scope

這次 refactor 不包含的事項描述。

## Further Notes (optional)

任何其他 refactor notes。

</refactor-plan-template>
