<a id="what-it-does"></a>

## 功能說明

當您沒有理解某則訊息時，請輸入 `wait-what`。[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 會重新說明它剛才說的話。它會補充您遺漏的背景資訊，使用簡潔的英文撰寫，並採用您專案 `GLOSSARY.md` 中的詞彙。

這個 skill 只有三行。這是設計，而非未完成的草稿。針對冗長問題的 skill 通常會失敗，因為它們變得過於長篇大論。一個四百行的簡潔 skill 仍會讓 [model](https://www.aihero.dev/ai-coding-dictionary/model) 保持冗長，因為模型會複製 skill 的長度，而非其要求。這個 skill 只有一個精確的引導詞，沒有其他內容。

<a id="when-to-reach-for-it"></a>

## 何時使用

您透過輸入 `/wait-what` 來呼叫它。agent 不會自行使用它，也不應該使用。只有您知道何時停止跟上了。

一旦您注意到自己正在略讀，就立即使用它。例如，agent 開始使用它自創的術語、在一個句子中放入五個縮寫，或解釋了一個您從未見過其前提的決策。它修復的是您已經身處的對話。若要完全阻止術語的出現，請使用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，它會事先建立共享語言。

<a id="the-name-is-the-mechanism"></a>

## 名稱就是機制

引導詞是 **wait**。"Be concise" 是關於 agent 輸出的指示，模型會透過刪減字詞來服從它，從而讓您更加迷失。**Wait** 是關於 *您* 的狀態。它表示您在此處停止理解了。聽到 "be brief" 的 agent 會寫出簡短的片段。聽到 "wait, you lost me" 的 agent 會退後並進行解釋。

這個差異就是整個 skill。所有針對冗長問題的流行修正方法都命名了 *輸出*：`/tldr`、`/no-fluff`、`/talk-normal`。模型會過度修正，變成更短但更不清晰的簡短片段。命名 *聽眾* 則同時要求兩部分：更少的字 **以及** 您遺漏的背景資訊。

這個 skill 說重新說明 **that**，而不是 "that last message"。讓您迷失的通常比一個段落更大，所以 agent 會決定要回溯多遠。

<a id="it-plugs-into-the-language-you-already-have"></a>

## 它融入您已有的語言

主體重用了您全域 `CLAUDE.md` 和專案 `GLOSSARY.md` 中已有的引導詞。ASD-STE100 簡化技術英文設定了語域。通用語言提供了名詞。這個 skill、`CLAUDE.md` 和 `GLOSSARY.md` 使用相同的 [tokens](https://www.aihero.dev/ai-coding-dictionary/token)，因此呼叫它不是一條新指示。它是對 agent 已同意的一條指示的提醒。

如果您沒有 `GLOSSARY.md`（也沒有指向當前情境的 `GLOSSARY-MAP.md`），這個 skill 仍然有效。您只會失去領域詞彙這一半。

<a id="its-working-if"></a>

## 如果符合以下條件，則表示它運作正常

- 重新說明是**更短且更清晰**，而不是更短且更生硬。
- 它補充了您遺漏的前提，而不只是刪除字詞。
- 專案名詞取代了自創的名詞。您 `GLOSSARY.md` 中的術語會回來。
- 您可以連續使用兩次，它不會退化成簡短生硬。

<a id="where-it-fits"></a>

## 適用位置

您可以在任何時間、任何對話、任何其他 skill 內部使用 `wait-what`。它在事後修復一則訊息。真正的修正是事先同意的共享語言，那就是 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)：一個在進行 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 會話時執行 [domain-modeling](https://aihero.dev/skills-domain-modeling) 的會話，以便記錄您雙方在 `GLOSSARY.md` 中使用的詞彙。如果您不確定哪個 skill 適合當下情境，[ask-matt](https://aihero.dev/skills-ask-matt) 會為您導向。
