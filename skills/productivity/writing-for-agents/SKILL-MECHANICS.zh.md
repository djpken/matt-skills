# Skill 機制

[`writing-for-agents`](SKILL.md) 裡 skill 特有的分支：當文件是一個 skill 時，什麼會不一樣——frontmatter、呼叫方式的選擇，以及路由器 skill。撰寫這份文件的其他部分，全都是 `SKILL.md` 裡那份通用參考。

## 呼叫方式

兩個選擇，各自換取兩種負載中的一種：

- **模型呼叫（model-invoked）**的 skill 保留一個 `description`，讓 agent 可以自主觸發它——而且其他 skill 也能觸及它。你仍然可以手動輸入它的名字：模型呼叫永遠*包含*使用者觸及；一個 description 只會增加 agent 的可發現性，永遠不會拿走人類的觸及方式。這個 description 是這個 skill 的頂層 context 指針，被強制常駐載入——用永久的 context 負載，換取可發現性。一個內容全是參考的模型呼叫 skill，同時也是共享參考的一個家：其他 skill 可以呼叫它，所以幾個 skill 都需要的參考就能住在同一個地方。機制：省略 `disable-model-invocation`，寫一個面向模型的 description，帶著觸發分支（`SKILL.md` 裡寫指針的規則在這裡完整適用）。
- **使用者呼叫（user-invoked）**的 skill 把 description 從 agent 的觸及範圍裡剝離：只有人類輸入它的名字才能呼叫它，其他 skill 也不行。零 context 負載，但要花認知負載——你是那個必須記得它存在的索引。機制：設定 `disable-model-invocation: true`；`description` 變成面向人類的——一行摘要，去掉觸發清單。

只有當 agent 必須自己觸及這個 skill，或另一個 skill 必須觸及它時，才選模型呼叫。如果它只會被手動觸發，讓它是使用者呼叫，不付 context 負載。

兩個使用者呼叫 skill 都需要的共享參考，不能住在其中任何一個裡——沒有 description，誰也無法觸發誰。把它推到 skill 系統之外的一份普通檔案：任何 skill 都可以指向的外部參考。

## 依呼叫方式拆分

拆分裡「依呼叫方式」的那一刀（依序拆分那一刀在 `SKILL.md` 裡）：當你有一個明確的主導詞、應該自己單獨觸發時，或另一個 skill 必須觸及它時，就拆出一個模型呼叫的 skill。你會為那個新的、常駐載入的 description 付出 context 負載，所以那份獨立的可觸及性必須值得。

## 路由器 skill

當使用者呼叫的 skill 多到你記不住時，那份堆積起來的認知負載，可以用一個**路由器 skill（router skill）**來治好：一個使用者呼叫的 skill，列出其他所有 skill 以及什麼時候該去用哪一個，讓人類只要記得一個 skill，不用記一堆。它只能提示，不能觸發它們：使用者呼叫的 skill 沒有 description，除了人類，誰都觸及不到它們。
