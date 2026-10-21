<a id="productivity"></a>

# 生產力

一般工作流程工具，非特定於程式碼。

<a id="user-invoked"></a>

## 使用者呼叫

僅在您輸入時可觸及（Claude Code：`disable-model-invocation: true`；Codex：`agents/openai.yaml` 中的 `policy.allow_implicit_invocation: false`）。

- **[grill-me](./grill-me/SKILL.md)**：針對計畫或設計進行嚴格的訪談，直到設計樹的每個分支都得到解決為止。
- **[handoff](./handoff/SKILL.md)**：將目前的對話濃縮為一份交接檔案，讓另一個 agent 能夠繼續進行工作。
- **[teach](./teach/SKILL.md)**：透過多個階段向使用者教授新的技能或概念，並使用目前目錄作為有狀態的教學工作區。
- **[to-questionnaire](./to-questionnaire/SKILL.md)**：將您無法獨自回答的決策轉換為 Markdown 問卷，交給唯一能回答的人（可非同步填寫，或在會議中共同完成）。
- **[wait-what](./wait-what/SKILL.md)**：當訊息未能傳達時立即觸發此指令。agent 會使用您缺少的脈絡，以簡潔的英文重新說明，並使用您的 `GLOSSARY.md` 詞彙。

<a id="model-invoked"></a>

## 模型呼叫

模型或使用者可觸及（使用豐富的觸發措辭，以便模型能使用它們）。

- **[grilling](./grilling/SKILL.md)**：針對計畫、決策或想法對使用者進行嚴格的訪談，直到設計樹的每個分支都得到解決為止。
- **[writing-for-agents](./writing-for-agents/SKILL.md)**：為 agent 撰寫檔案：skill、AGENTS.md/CLAUDE.md，以及 agent 透過指標可達到的任何檔案。
