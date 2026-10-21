# 在中文分支直接使用繁體中文 Markdown

使用者已確認，`zh-TW` 分支的 README、skill 主檔與參考文件直接使用標準 `.md` 檔名承載台灣繁體中文，不再保留同內容的語系後綴副本。這會讓 agent 直接讀取中文 `SKILL.md`。英文版本保留在 `main` 與 Git history，中文文件索引提供變更前英文快照。

此決策取代原本的雙語對應檔策略。好處是使用者與 agent 都直接讀取中文；代價是合併上游英文更新時，需要重新核對並翻譯受影響文件。上游同步應保留 skill 的條件、工作流程與安全邊界，不只核對表面字詞。

保留 `name:` 與其他呼叫欄位、程式碼區塊、inline code、指令、路徑、指定輸出字串與 plugin manifest。只調整因檔名變更而需要更新的文件連結。原有英文標題錨點保留，既有片段連結仍可使用。

`AGENTS.md`、`CLAUDE.md`、`RELEASE-NOTES.md` 與 changelog 沿用目前內容。
