---
name: handoff
description: 將目前對話壓縮成交接文件，供另一個 agent 接手工作。
argument-hint: "下一個 session 將用來做什麼？"
disable-model-invocation: true
---

寫一份總結目前對話的交接文件，讓新的 agent 能夠繼續工作。將其儲存到使用者 OS 的臨時目錄中——不是目前工作區。

在文件中包含「建議的 skills」部分，建議 agent 應該呼叫哪些 skills。

不要複製已捕獲在其他成品中的內容（PRD、計畫、ADR、issues、commits、diffs）。改為用路徑或 URL 引用它們。

刪除任何敏感資訊，例如 API 金鑰、密碼或個人識別資訊。

如果使用者傳遞了參數，將其視為下一個 session 將關注的描述，並相應地調整文件。
