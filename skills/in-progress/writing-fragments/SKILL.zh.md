---
name: writing-fragments
description: 寫作 explore：挖出原始 fragments，先不套結構。
disable-model-invocation: true
---

<what-to-do>

這是純粹的 **explore**：擴大可能被寫出的空間，不承諾結構；承諾結構是 _exploit_，屬於另一個 skill。執行 grilling session，無情訪談使用者想寫的主題，產出 fragments。強加階段、outline 或文章結構都超出此處範圍。

當 fragments 從對話任一方浮現時，append 到單一 Markdown 檔。

如果使用者沒有傳路徑，問一次文件要存在哪裡，然後在 session 剩餘時間記住它。

從使用者說的第一件事開始捕捉 fragments，包含初始 prompt。

第一次寫入時，在頂部放一個 H1 working title（之後可改），除此之外不要放任何東西：沒有 metadata、沒有 TOC、沒有日期。

</what-to-do>

<supporting-info>

## What is a fragment

Fragment 是任何可能存活到最終文章裡的文字片段。它必須能被**作者讀懂**：作者知道它是什麼意思；但它不需要定義術語，也不需要讓冷讀者理解。標準是「這是不是一段好文字？」，不是「這是不是自成一體的論證？」

Fragments 刻意異質。可能成為 fragment 的例子：

- 一句你想在某處使用但還不知道放哪裡的 sharp sentence。
- 一個 claim 加上一行 justification。
- 一段 vignette：發生過的事、code snippet、scenario、analogy。
- 半個想法：「something about how X feels like Y, work this out later.」
- 一段 quote、dialogue、overheard line。
- 一組感覺上相關的 observations。
- 一段 complaint、confession、punchline。
- 一個 **leading word**：整篇文章可以掛在上面的緊湊比喻或造詞（一個命名該想法的 term，就像 _tracer bullets_ 或 _fog of war_ 命名整個 pattern）。

其中，leading word 是最有價值的 fragment。它承重：在 explore 階段命名對了，之後會塑造結構、轉場和標題，在整個 exploit phase 產生回報。當對話繞著一個反覆出現的想法打轉時，推動使用者為它鑄造一個詞。

小說家的日記是模型：多年無結構的 noticings，之後被挖掘為原始素材。Fragments 就是 noticings。

## File format

```markdown
# Working title

A first fragment lives here.

It can be multiple paragraphs. It can include lists, code, quotes — whatever
shape the fragment naturally takes.

---

A second fragment.

---

> A quoted line that the user wants to keep around.

A reaction to it.

---

- A cluster of related observations
- That hang together by feel
- And want to be near each other
```

Fragments 以 horizontal rule（`\n---\n`）分隔。Body 內不要 headings。不要 tags。除了加入順序外不要排序。

## Writing rhythm

安靜地 append。不要每個 fragment 都問 permission。順口提到你加了什麼（「adding that」），但不要用 save dialogs 打斷對話。

每次寫入前：從磁碟重新讀取檔案。使用者可能在 turns 之間編輯、重排或刪除 fragments；保留他們的改動。永遠不要覆寫檔案；只 append（或在使用者要求時，原地編輯特定 fragment）。

使用者可以隨時說「cut the last one」、「rewrite that one sharper」、「merge those two」。把這些視為一等指令。

</supporting-info>
