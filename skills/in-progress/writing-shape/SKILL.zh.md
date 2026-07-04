---
name: writing-shape
description: 寫作 exploit：將原始素材逐段塑形成文章。
disable-model-invocation: true
---

<what-to-do>

使用者已經傳入（或將傳入）一個原始素材 Markdown 檔。把它視為 input pile：它可以是整理好的 fragments 清單、無結構散文牆，或 transcript。格式不重要。先從頭到尾讀完。

接著執行 shaping session，產出一份獨立的文章文件。這是 **exploit**：探索已完成，素材堆固定；承諾一個結構，並從素材堆中挖材料填入。不要編輯原始素材檔，這個 skill 對它只讀。

如果使用者沒有說文章要存在哪裡，問一次並記住路徑。

</what-to-do>

<supporting-info>

## The loop

1. **Read the pile.** 完整讀取 input file。建立對素材內容的感覺。
2. **Establish the prerequisites.** 與使用者決定讀者進來時知道什麼，也就是一開始就 **grounded** 的概念。其他所有概念都必須先由某個 block grounding，後續 block 才能倚賴它。見 [Grounding](#grounding)。
3. **Draft 2–3 candidate openings.** 每個 opening 都應暗示文章不同 thesis 或 angle。全部展示。強制使用者選一個或組合 hybrid。被選中的 opening 定義文章剩餘部分必須完成什麼。
4. **Grow paragraph by paragraph.** Opening 落地後，問「given this opening, what does the reader need to hear next?」從素材堆拉材料回答。下一個 block 只能倚賴已 grounded 的 concepts，並在落地時 grounding 新 concepts。與使用者爭論下一個 block 的形式：paragraph、list、table、callout、quote、code block。每個 format choice 都應刻意且可辯護。
5. **Append to the article file as you go.** 不要 batch。每個同意的 paragraph 或 block 立即寫入，讓使用者能看見文章成形。
6. **Loop step 4 until the article is done.** 由使用者決定何時完成。

## Grounding

每個 **concept** 都必須先 **grounded**，block 才能倚賴它：讀者要嘛進來前就知道，要嘛在更早的 block 遇過。倚賴 ungrounded concept 的 block 會弄丟讀者。單位是 concept，不是它的字詞；即使沒有術語，block 也可能倚賴讀者缺少的想法。當概念有名稱，也就是 **term**，grounding 它代表同時落地想法與術語。

Concept 有兩種 grounded 方式：

- **Prerequisite**：opening 前就 grounded。讀者帶著它進來。一開始固定。
- **Introduced**：某個 block 建立它，從此之後它對文章剩餘部分都 grounded。

持續維護目前 grounded 的清單。當你問「what does the reader need to hear next?」時，如果下一步需要 ungrounded concept，那個 concept 本身就是答案：先在這裡或更早的 block grounding 它，否則不能走這步。這是 [Pulling from the pile](#pulling-from-the-pile) 的 gap-naming 往上一層：那裡是素材堆缺材料；這裡是文章缺 foundation。

槓桿在於哪些概念列為 prerequisite，哪些要在文章內 grounding。前置要求太多會把讀者擋在門外；文章內 grounding 太多會讓 opening 淹沒在定義裡。建立 prerequisites 時與使用者決定。

## Conversational feel

這是反過來的 grilling session。在 ideation 中，問題是「你實際注意到了什麼？」這裡則是「這篇文章實際在主張什麼，讀者需要按什麼順序聽到？」Push back。拒絕放過弱轉場。如果 paragraph 沒有賺到自己的位置，就砍掉。

持續使用這些具體 moves：

- "What does this paragraph do for the reader that the previous one didn't?"
- "If I cut this, what breaks?"
- "Is this prose, or should it be a list? Why prose?"
- "This sentence is doing two jobs — split it or pick one."
- "The opening promised X. We've drifted to Y. Either re-thread it or change the opening."

## Pulling from the pile

把原始素材當 quarry，不是 script。拉一個 fragment，重寫到適合周圍 paragraph，然後放進去。Fragment 可以拆到多段、和其他 fragment 合併，或 paraphrase。素材堆的工作是被挖掘；文章的工作是讀起來像同一個聲音。

如果素材堆缺少文章需要的東西，明確指出 gap：「We need an example here and the pile doesn't have one — give me one now or we cut this section.」

## Format arguments to actually have

選擇如何呈現 block 時，和使用者大聲權衡這些 tradeoffs，不要默默決定：

- **Prose vs. list.** Prose 承載論證；lists 承載平行 items。如果 items 不是真正平行，prose 較好。如果是，list 掃描更快。
- **Inline vs. callout.** Tips、warnings、asides 放在 callouts（`> [!TIP]`、`> [!NOTE]`），但只有在 inline 會真正打斷主論證時才這樣做。否則留在 inline。
- **Table vs. repeated structure.** 如果同一形狀以相同欄位重複 3+ 次，用 table。否則用帶 bold leads 的 prose。
- **Quote vs. paraphrase.** 原始措辭本身重要時 quote；只有 idea 重要時 paraphrase。
- **Code block vs. inline code.** 多行、可執行或示範用途 → block。單一 token 或 identifier → inline。

## Writing rhythm

每個 block 同意後 append 到文章檔。每次寫入前從磁碟重新讀取檔案；使用者可能在 turns 之間編輯。永遠不要盲目覆寫。如果使用者想重寫 paragraph，原地編輯那個特定 paragraph；其餘部分別動。

## Out of scope

- 挖掘素材堆中不存在的新 fragments（依「Pulling from the pile」處理 gaps）。
- 編輯原始素材檔。
- 發布、特定平台格式化，或加入使用者沒要求的 frontmatter。

</supporting-info>
