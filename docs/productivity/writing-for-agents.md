<a id="what-it-does"></a>

## 功能說明

`writing-for-agents` 是撰寫面向 agent 檔案的參考標準：一個 skill、一個 `AGENTS.md` 或 `CLAUDE.md`、一個 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)、一個 runtime prompt、一個 README，或任何 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 會讀取的檔案。格式雖然不同，但撰寫方式相同。相同的機制讓每一份檔案都可預測，因此 agent 在每次執行時都遵循相同的 *process*（不一定產生相同的輸出）。

其預設修正是刪除，而非解釋。要求 agent 為另一個 agent 撰寫指令時，它會花大部分的字數來解釋 [model](https://www.aihero.dev/ai-coding-dictionary/model) 已經知道的事情。這些行中的每一行都是 **no-op**：它消耗 [context](https://www.aihero.dev/ai-coding-dictionary/context) 但不改變任何行為。此參考有助於您找出這些內容，因此無論是在您已有的檔案上，還是在空白檔案上，它同樣有用。

在 v1.1 之前，它被稱為 `writing-great-skills`。新名稱更符合它一直以來本質。其中幾乎沒有內容是特定於 skill 的。僅限 skill 的機制（frontmatter、model 呼叫與使用者呼叫的選擇、router skills）位於一個連結的 `SKILL-MECHANICS.md` 中，您只需在面前的檔案是 skill 時才閱讀它。

<a id="when-to-reach-for-it"></a>

## 何時使用

輸入 `/writing-for-agents`，或者當您建立或編輯 skill，或修改 `AGENTS.md` 或 `CLAUDE.md` 時，agent 會自行使用它。

對於 agent 讀取的其他所有內容，請手動使用它：您的檔案、spec 和 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)、system 和 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk) prompts。測試只有一個問題：agent 是否讀取此檔案？agent 如何取得檔案並不重要：是指標指名它、人類貼上它，還是它在儲存庫中。若要了解程式碼庫包含什麼，請使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)。此參考控制檔案的讀取方式，而非它知道什麼。

<a id="the-two-loads"></a>

## 兩種負載

核心概念是每個檔案和指標都會消耗的兩個預算：

- **Context load**：agent 視窗中常駐載入材料的成本：一行 `AGENTS.md`、一個 skill 描述，或任何在每個 [turn](https://www.aihero.dev/ai-coding-dictionary/turn) 都位於 context 中的內容，無論它是否觸發。
- **Cognitive load**：您記住哪些檔案存在以及何時使用每個檔案的成本。您就是索引。不要試圖最小化此成本，因為這是人類自主性的代價。

一旦您以這兩種負載來思考，大多數撰寫決策（分割或不分割、內嵌或揭露、指向或推送）都變成在不同地方進行的相同取捨。

<a id="the-levers"></a>

## 機制

- **[Context pointers](https://www.aihero.dev/ai-coding-dictionary/context-pointer)**：保留在 context 中並指名 context 外材料、同時編碼何時使用它的參考。一個 skill 描述和一行指名檔案的 `AGENTS.md` 是同一件事。指標的 *措辭*，而非其目標，決定了 agent 遵循它的可靠程度。
- **Information hierarchy**：從檔案內步驟，到檔案內參考，再到指標後揭露參考的階梯。**[Progressive disclosure](https://www.aihero.dev/ai-coding-dictionary/progressive-disclosure)** 是將材料沿該階梯向下移動，使頂層保持易於閱讀。
- **Completion criteria**：每個步驟的完成條件有多清晰和嚴格，以及該要求造成的 **legwork**。它們是對抗 **premature completion** 的防禦。
- **Leading words**：模型預訓練中已存在的緊湊概念（*tight*、*red*、*tracer bullet*），agent 在執行檔案時會以此進行思考。它在兩個地方起作用。在正文中，它引導執行；在指標中，它觸發呼叫。
- **Pruning**：單一事實來源、相關性，以及逐句應用的 no-op 測試，以對抗 **duplication**、**sediment** 和 **sprawl**。

<a id="common-questions"></a>

## 常見問題

**`/writing-great-skills` 去哪了？**
這就是此 skill，在 v1.1 中重新命名。使用者在重新命名之前很久就將它指向 `AGENTS.md`、檔案、spec、tickets 和 runtime prompts。結構、leading words 和 pruning 適用於 agent 讀取的任何文字。沒有別名。請在新名稱下重新安裝。

**"Writing for agents"：所以是 agent 來撰寫？**
恰恰相反。您是作者；agent 是讀者。這就是困難所在。您為一個已經讀過一切的讀者撰寫，因此解釋是浪費，精確才是全部工作。

**我能不能直接要求 agent 幫我寫？**
您可以，但它會產生冗長的內容。放任不管，模型會解釋它已經知道的事情，而且它不會自行應用 no-op 測試或尋找 leading word。使用此參考來審查草稿。其大部分價值來自該審查過程。

**我要求 agent 刪減檔案，但它刪除了功能。**
被告知要"streamline"的 agent 會針對長度進行最佳化，因為長度是它們能看到的東西。No-op 測試檢查的是行為，而非風格：刪除該行並詢問 agent 的行為是否改變。當一個句子未透過測試時，刪除整個句子，而不是從中刪減字詞，並透過執行檔案而非爭論來解決對它的分歧。

**我怎麼知道何時完成？**
當它運作正常，且您再也找不到 duplication、sediment 或 no-ops 時。沒有自動化的 eval。您透過手動執行檔案並使用 failure-mode 詞彙來診斷問題來檢查。當檔案行為異常時，相同的詞彙有助於您修復它：先指名 failure mode，然後修復該問題。

**這應該放在 `CLAUDE.md` 還是其他地方？**
詢問您想支付哪種負載。`CLAUDE.md` 會無條件地載入到每個 [session](https://www.aihero.dev/ai-coding-dictionary/session) 中。指標後的材料在觸發之前只消耗指標本身的行數。任何在十個 session 中只有一個適用的內容，會在其他九個中消耗 context load。

**我需要為每個新模型重寫我的檔案嗎？**
大多數情況下不需要，而且過度擬合單一模型會帶來其自身問題。為新模型更新通常只是另一輪 no-op 檢查，而非重寫。

**我的 skill 只在我建立它的那個精確任務上運作。**
常見方法（做一次工作，然後讓 agent 將其撰寫為 skill）過於貼合那一次執行，導致範例過於具體。保留該執行作為證據，然後有意地進行泛化：剝離屬於該儲存庫和那些檔案的內容，並為該類任務撰寫。

**英文不是我的母語。我會失去 leading-word 的優勢嗎？**
不會。找到能用最少 [tokens](https://www.aihero.dev/ai-coding-dictionary/token) 包含最多行為的詞，是此參考為您完成的工作。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它運作正常

- 檔案隨著變好而變短，您會驚訝於剩餘的內容有多少。
- 您可以指向一個 leading word，並看到它在多個地方改變 agent 的行為。
- 任何內容都不以任何形式重複陳述。Duplication 是檔案從未經過測試的最可靠跡象。
- 參考資料指出，只有一個分支需要位於指標後方，而非主檔案中。

<a id="where-it-fits"></a>

## 適用位置

這是一份隨時可查閱的獨立參考資料。它適用於整個集合，而非單一 skill。此處的每個 skill 都是依據它撰寫的，它也涵蓋其他 skill 所產生的檔案（例如 `GLOSSARY.md` 及其 ADR、規格說明、工單），一旦 agent 需要閱讀這些檔案時便適用。它唯一的直接呼叫者是 [retro](https://aihero.dev/skills-retro)，該 skill 會在提出任何 steering file 或 skill 之前載入此參考資料。當您不確定哪個 skill 或流程適合某項任務時，[ask-matt](https://aihero.dev/skills-ask-matt) 會引導您瀏覽整個集合。
