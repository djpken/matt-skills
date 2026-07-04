---
name: writing-beats
description: 寫作 exploit：將原始素材組成一段 beat 旅程，每個 beat 使用術語前都先 grounding。
disable-model-invocation: true
---

<what-to-do>

使用者已經傳入（或將傳入）一個原始素材 Markdown 檔。這是 **exploit**：探索已完成，素材堆固定；選定一條路徑，從素材堆中挖出內容填入每個 beat。

如果使用者沒有說文章要存在哪裡，問一次並記住路徑。

接著執行 beat-by-beat、choose-your-own-adventure 風格的旅程：

1. **Establish the prerequisites.** 在任何 beats 前，與使用者決定讀者一開始已經知道什麼，也就是從一開始就已 **grounded** 的概念。其他所有概念都必須先由某個 beat grounding，後續 beat 才能倚賴它。見 [Grounding](#grounding)。
2. 寫 2–3 個候選 **starting beats**，都從原始素材抽出。每個都是文章的不同入口。每個只能倚賴已 grounded 的概念；註明它 grounding 了哪些新概念。先展示 beats，不要直接寫入文章檔。使用者選一個。預覽這個選擇會解鎖哪些 beats，就像讓使用者看見路徑往下的一小段。
3. 使用者選定 starting beat 後，只把**那個 beat**寫入文章檔。Beat 可以是一句話或數段，按它自然需要的大小。停在這裡。
4. 從磁碟重新讀取文章檔。然後提供 2–3 個候選 **next beats**：文章可從目前位置轉向的不同方向。每個都必須能從目前 grounded set 抵達；註明它 grounding 了什麼。
5. 重複步驟 3–5，直到文章抵達自然結尾。

</what-to-do>

<supporting-info>

## Grounding

每個 **concept** 都必須先 **grounded**，beat 才能倚賴它：讀者要嘛進來前就知道，要嘛在更早的 beat 遇過。倚賴 ungrounded concept 的 beat 會弄丟讀者，這是這段旅程不能做的動作。單位是 concept，不是它的字詞：即使沒有術語，beat 也可能倚賴讀者缺少的想法。當概念有名稱，也就是 **term**，grounding 它代表同時落地想法與術語。

Concept 有兩種 grounded 方式：

- **Prerequisite**：第一個 beat 前就 grounded。讀者帶著它進來。一開始固定。
- **Introduced**：某個 beat 建立它，從此之後它對所有後續 beat 都 grounded。

所以每個 beat 做兩件事：它 **requires** 已 grounded 的 concepts，也 **grounds** 新 concepts。持續維護目前已 grounded 的清單，並在每個 beat 落地後更新。

這會塑造 choose-your-own-adventure。候選 beat 只有在它需要的一切都已 grounded 時才可抵達；選擇 grounding concept X 的 beat，會解鎖所有原本等著 X 的 beats。提供 next beats 時，它們都必須可從目前 grounded set 抵達，並說明每個會 grounding 什麼，讓使用者看見它會打開哪些路徑。

最大的槓桿是哪些概念列為 prerequisite，哪些要在文章內 grounding。前置要求太多會排除沒有背景的讀者；文章內 grounding 太多會讓早期 beats 淹沒在定義裡。建立 prerequisites 時要和使用者決定；每當誘人的 beat 需要尚未 grounded 的概念時也要重訪：修法要嘛先寫 grounding beat，要嘛把該概念提升為 prerequisite。

## What is a beat

Beat 是旅程中的一個動作。它只做一件事：設場景、落一個點、問一個問題、插一段 aside、扭轉角度。然後停下，讓讀者抵達一個下一個 beat 可以轉向的位置。

Beat 大小由需求決定：

- 如果動作只需要一句話，那就是一句話（"And then nothing happened for three weeks."）。
- 如果動作需要 setup，就是一小段。
- 如果 beat 是自成一體的 vignette、argument 或 example，可以是多段。

如果一個「beat」需要五段和三個 subheadings，它就不是 beat，而是兩個 beats 黏在一起。拆開。

## Pulling from the pile

從原始素材堆中拉材料填入每個 beat。可以 paraphrase、拆分、重組或引用。素材堆是一座 quarry。

## Ending the journey

文章在旅程完整時結束，不是在素材堆用完時結束。大多數素材堆都會剩下不進文章的 fragments。這很好；這正是素材比所需更多的意義。

## Writing rhythm

- 一次 append 一個 beat。永遠不要超前寫。
- 每次寫入前，從磁碟重新讀取文章檔。絕對保留使用者編輯。
- 如果使用者大幅修改前面的 beat，讓它改變接下來會發生什麼。
- 如果使用者說「rewrite that beat」或「go back and try a different beat 3」，照做；原地編輯，其他部分別動。

</supporting-info>
