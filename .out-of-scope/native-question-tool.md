<a id="grilling-through-the-harnesss-native-question-tool"></a>

# 透過 harness 的原生問題工具進行 Grilling

Grilling 工作階段（`/grilling`、`/grill-me`、`/grill-with-docs`，以及其他 skill 內的 grilling）會以純文字聊天形式提出問題。要求將這些問題透過 harness 內建的問題 UI（例如 Claude Code 的 `AskUserQuestion`、host 的「問題工具」、結構化或批次問卷模式）進行路由，不在支援範圍內。

<a id="why-this-is-out-of-scope"></a>

## 為何此功能不在支援範圍內

這些 skill 是與 harness 無關的文字。它們可在 Claude Code、Codex、Copilot、Cursor 以及 skills.sh 安裝的任何其他環境中執行，而每個 harness 都有不同問題工具，甚至可能沒有問題工具。若在 skill 中指定某個 harness 的工具，則必須在每個 grilling skill 中加入特定 harness 的分支，或者該 skill 只能在單一環境中正常運作。

問題 UI 也會改變 grilling 本身的性質。結構化選擇器會引導模型提出帶有預設答案的多選題，這與 grilling 的目的背道而馳：grilling 旨在提出開放式問題，讓使用者以自己的語言回答，一次處理決策樹的一個分支。我試過 `AskUserQuestion` 的 UI，不希望這些 skill 使用它。

如果您偏好使用您 harness 的問題工具，請在您自己的 `CLAUDE.md` / `AGENTS.md` 中說明（例如：「在 grilling 時，透過 AskUserQuestion 提問」）。這是一行針對個別使用者的偏好設定，不需要共用的 skill 知道此事。

<a id="prior-requests"></a>

## 先前請求

- #19：「grill-me：並非總是使用問題工具」
- #643：「提案：在 grilling 工作階段期間使用 AskQuestion 進行結構化輸入」
- #840：「在 grilling 工作階段中使用 host 的問題工具」
- #1107：「Grilling 預設未使用 Agent 的原生 QA 工具」
- #1152：「提案：實驗性 HTML 問卷模式，用於批次 grilling」
