---
name: handoff
description: "將目前的對話壓縮成一份交接檔案，供另一個 agent 接手。"
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

撰寫一份交接檔案，摘要目前的對話，讓新的 agent 能夠繼續工作。儲存至使用者作業系統的暫存目錄（`$TMPDIR`，否則為 `/tmp`；Windows 上為 `%TEMP%`），而非目前的儲存空間。

在檔案中包含「建議的 skill」章節，指明下一個 agent 應為哪些 skill 呼叫 Skill 工具。

不要重複其他產出物（規格、計畫、ADR、問題、commit、diff）中已記錄的內容。請改用路徑或 URL 來引用它們。

移除任何敏感資訊，例如 API 金鑰、密碼或個人識別資訊。

如果使用者傳入了引數，請將其視為對下一個工作階段重點的描述，並據此調整檔案內容。
