# Matt Pocock Skills

一組由 Claude Code 載入的 agent skills（斜線指令與行為）。這些 skills 依類別分組，並由 `/setup-matt-pocock-skills` 產生的每個儲存庫設定所使用。

<a id="language"></a>

## 語言

**Issue tracker**：
託管儲存庫 issues 的工具：GitHub Issues、Linear、本機 `.scratch/` markdown 慣例，或類似工具。像 `to-tickets`、`to-spec` 和 `triage` 這樣的 skills 會從中讀取並寫入。
_避免使用_：backlog manager、backlog backend、issue host

**Issue**：
**Issue tracker** 內單一追蹤的工作單位：一個 bug、task、spec，或由 `to-tickets` 產生的 slice。
_避免使用_：ticket（僅在引用將其稱為 ticket 的外部系統時，或用於 **Decision ticket** 時使用，詳見下文）

**Decision ticket**：
一個 `wayfinder` 單位：`wayfinder:map` 的子 **Issue**，包含一個 *question*，其解決方式是一項決策，而非待執行的建置 slice。**decision** 修飾詞使其與實作 ticket 有所區別；`wayfinder` 引入此術語，隨後使用 "ticket"。

**Triage role**：
在 triage 期間套用至 **Issue** 的標準狀態機標籤（例如 `needs-triage`、`ready-for-afk`）。每個 role 透過 `docs/agents/triage-labels.md` 對應至 **Issue tracker** 中的實際標籤字串。

<a id="relationships"></a>

## 關係

- 一個 **Issue tracker** 包含多個 **Issues**
- 一個 **Issue** 同時只帶有一個 **Triage role**
- 一個 **Decision ticket** 是一個 **Issue**（`wayfinder:map` 的子項）

<a id="flagged-ambiguities"></a>

## 已標記的歧義

- "backlog" 先前同時用於指代託管 issues 的*工具*以及其中的*工作內容*。已解決：該工具稱為 **Issue tracker**；"backlog" 不再作為領域術語使用。
- "backlog backend" / "backlog manager"。已解決：合併為 **Issue tracker**。
