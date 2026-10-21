---
name: claude-handoff
description: "將目前的對話移交給一個全新的背景 agent，該 agent 會立即接手工作。"
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

撰寫目前對話的交接摘要，以便新的 agent 能繼續工作。將其儲存至使用者作業系統的暫存目錄，然後啟動一個背景 agent，並以該摘要作為其提示詞：`claude --bg --name "<descriptive name>" -- "$(cat <summary file>)"`。傳遞檔案可避免 shell 執行摘要中的反引號或展開 `$`。它會在目前的工作目錄中啟動並立即返回；使用者使用 `claude agents` 來管理它。

傳遞 `-n`/`--name` 時，務必使用描述性的名稱（例如 `--name "Fix login bug"`）；這會設定在任務清單、工作區選擇器以及終端機標題中顯示的名稱。

在摘要中包含「建議的 skills」章節，指明下一個 agent 應該為哪些 skills 呼叫 Skill 工具。

不要重複其他產出物（規格、計畫、ADR、問題、commit、diff）中已記錄的內容。請改用路徑或 URL 來引用它們。

移除任何敏感資訊，例如 API 金鑰、密碼或個人識別資訊，因為該摘要將成為 agent 的提示詞。

如果使用者傳遞了引數，請將其視為對下一個工作區將專注內容的描述，並據此調整摘要。
