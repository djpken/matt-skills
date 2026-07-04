---
name: claude-handoff
description: 將目前對話交接給一個新的背景 agent，讓它立即接手工作。
argument-hint: "下一個 session 將用來做什麼？"
disable-model-invocation: true
---

撰寫目前對話的 handoff summary，讓新的 agent 可以繼續工作。不要把它存成檔案；改成啟動一個以 summary 作為 prompt 的背景 agent：`claude --bg --name "<descriptive name>" "<handoff summary>"`。它會從目前 working directory 開始並立即返回；使用者用 `claude agents` 管理它。

一定要傳入 `-n`/`--name` 並使用描述性名稱（例如 `--name "Fix login bug"`），這會設定 job list、session picker 和 terminal title 中顯示的名稱。

在 summary 中包含 "suggested skills" section，建議該 agent 應呼叫哪些 skills。

不要複製已捕捉在其他 artifacts 中的內容（PRDs、plans、ADRs、issues、commits、diffs）。改用 path 或 URL 引用它們。

遮蔽任何敏感資訊，例如 API keys、passwords 或 personally identifiable information；summary 會變成 agent 的 prompt。

如果使用者傳入 arguments，把它們視為下一個 session 會聚焦的內容描述，並據此調整 summary。
