<a id="engineering"></a>

# 工程

我在程式碼工作中每日使用的技能。

<a id="user-invoked"></a>

## 使用者呼叫

僅在您輸入時可觸及（Claude Code：`disable-model-invocation: true`；Codex：`agents/openai.yaml` 中的 `policy.allow_implicit_invocation: false`）。

- **[ask-matt](./ask-matt/SKILL.md)**：詢問哪項技能或流程適合您的情況。這是本儲存庫中使用者呼叫技能的路由器。
- **[grill-with-docs](./grill-with-docs/SKILL.md)**：一場嚴謹的問答會議，同時建立專案的領域模型，精煉術語並即時更新 `GLOSSARY.md` 和 ADR。
- **[triage](./triage/SKILL.md)**：透過分級角色的狀態機來處理問題。
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)**：掃描程式碼庫以尋找深化機會，將其呈現為視覺化 HTML 報告，然後針對您選擇的專案進行嚴謹問答。
- **[setup-matt-pocock-skills](./setup-matt-pocock-skills/SKILL.md)**：為工程技能設定此儲存庫（問題追蹤器、分級標籤、領域檔案佈局）。每個儲存庫只需執行一次。
- **[to-spec](./to-spec/SKILL.md)**：將目前的對話轉為規格說明並發布至問題追蹤器。
- **[to-tickets](./to-tickets/SKILL.md)**：將任何計畫、規格說明或對話拆解為一組曳光彈工單，每個工單都宣告其阻塞邊緣，形式可以是本地檔案中的文字，也可以是真實追蹤器上的原生阻塞連結。
- **[implement](./implement/SKILL.md)**：建置由規格說明或工單集所描述的工作，在預先協定的接縫處驅動 `/tdd`，並在提交前以 `/code-review` 收尾。
- **[implement-spec](./implement-spec/SKILL.md)**：在單一整合分支上實作整個規格說明。將工單視為任務圖，在就緒前沿執行實作者子代理以達到最大並行度，然後以 `/code-review` 收尾。
- **[wayfinder](./wayfinder/SKILL.md)**：將大量工作（超過單一代理會話所能容納的範圍）規劃為問題追蹤器上決策工單的共享地圖，逐一解決，直到通往目的地的路徑清晰為止。
- **[retro](./retro/SKILL.md)**：在會話結束後，建議改善編碼代理的環境（導航、自動化檢查、編碼標準、引導檔案、工具），最嚴重的問題優先。

<a id="model-invoked"></a>

## 模型呼叫

模型或使用者可觸及（使用豐富的觸發措辭，以便模型能使用它們）。

- **[prototype](./prototype/SKILL.md)**：建置一次性原型以回答設計問題：用於狀態/邏輯的單一可分享 HTML 檔案，或數個可切換的 UI 變體。

- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)**：針對困難的錯誤和效能退化的紀律性診斷迴圈：建立一個在此錯誤上變紅的回饋迴圈 → 最小化 → 假設 → 插樁 → 修復 → 回歸測試。
- **[research](./research/SKILL.md)**：針對高信任度的主要來源調查問題，並將發現記錄為儲存庫中帶有引用的 Markdown 檔案，作為背景代理執行。
- **[tdd](./tdd/SKILL.md)**：採用紅綠重構迴圈的測試驅動開發。一次建置一個垂直切片的功能或修復一個錯誤。
- **[domain-modeling](./domain-modeling/SKILL.md)**：透過挑戰術語、使用情境進行壓力測試，並即時更新 `GLOSSARY.md` 和 ADR，來主動建立和精煉專案的領域模型。
- **[codebase-design](./codebase-design/SKILL.md)**：設計深度模組的共享紀律和詞彙：小型介面、乾淨的接縫、可透過介面進行測試。
- **[code-review](./code-review/SKILL.md)**：對自固定點以來的差異進行雙軸審查：**標準**（是否遵循儲存庫的編碼標準，加上 Fowler 異味基準？）和**規格**（是否忠實實作原始問題/規格？），作為並行子代理執行。
- **[pr](./pr/SKILL.md)**：拉取請求主體應有的形式：摘要作為使變更清晰的最小視覺元素、證明其運作的前後證據，以及合併風險評估（單向或雙向門，加上影響範圍）。
- **[wizard](./wizard/SKILL.md)**：產生互動式 bash 精靈，引導人類完成僅由他們執行的步驟：設定基礎設施、設定憑證或 CI 金鑰、導覽不熟悉的新第三方儀錶板，或執行一次性遷移或切換。
