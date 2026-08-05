---
name: wayfinder
description: 規劃一大塊工作——大到超過一個 agent session 能裝下——把它變成 issue tracker 上一張共享的決策 ticket 地圖，逐一解決，直到通往目的地的路徑清楚為止。
disable-model-invocation: true
---

一個鬆散的想法出現了——大到一個 agent session 裝不下，而且被迷霧包住：從這裡到**目的地（destination）**的路還看不見。Wayfinding 是要找出那條路，而不是直接衝向目的地。這個 skill 會在 repo 的 issue tracker 上把路徑繪成一張**共享地圖（shared map）**，然後一次處理一張**決策 ticket（decision ticket）**——這些是「解決方式是一個決策」的問題，而不是要執行的建置切片——直到路線清楚。

每次的目的地都不同，把它命名出來就是繪圖的第一步——它會塑造每一張 ticket。目的地可能是要交出去反覆迭代的一份 spec、開工前要鎖定的一個決策，或是像資料結構遷移那樣就地完成的改動。地圖與領域無關——engineering work、course content，任何符合這個形狀的工作都適用。

## 規劃，而非動手

Wayfinder 預設是在**規劃（planning）**：每張 ticket 解決一個決策，地圖完成的定義是路徑清楚——在有人真的去做這件事之前，沒有東西還需要決定。想直接動手做的衝動，通常正是你已經到達地圖邊界、該交接出去的訊號。一次效力可以在自己的 **Notes** 裡覆寫這個預設——把執行本身也帶進地圖——但沒有特別說明時，產出的是決策，而不是交付物。

## 用名字引用

每張地圖和每張 ticket 都是一個 issue，所以都有**名字**——也就是它的 title。在所有人類會讀到的地方——敘述、地圖的 Decisions-so-far——都用名字引用，不要只用裸 id、編號或 slug。滿牆的 `#42, #43, #44` 讓人看不懂；名字一眼就能懂。Id 和 URL 不會消失——名字會把 link 包起來——但它們只搭在名字*裡面*，不能取代名字。

## 地圖（The Map）

地圖是這個 repo issue tracker 上的一個 issue，標籤是 `wayfinder:map`——它是 canonical artifact。它的 tickets 是這張地圖的 child issues。

地圖是一個 **index**，不是 store。它列出已做的決策，並指向保存細節的 tickets；一個決策只存在於一個地方——它的 ticket——所以地圖從不重述決策，只給 gist 和 link。

**地圖、它的 child tickets、blocking 以及 frontier 查詢實際存放的位置，取決於 tracker。** Issue tracker 應該已經提供給你——如果沒有，執行 `/setup-matt-pocock-skills`。查閱 tracker 文件的 "Wayfinding operations" 段落，看*這個* repo 如何表達它們。如果沒有提供任何 tracker，預設使用 local-markdown tracker。

### 地圖本文

整張地圖以低解析度呈現，每個 session 載入一次。Open 的 tickets **不**列在地圖本文裡——它們是 open 的 child issues，用查詢找出來。

```markdown
## Destination

<抵達這張地圖終點會是什麼樣子——這次效力正在尋路前往的 spec、決策或改動。一到兩行；每個 session 在選 ticket 之前都先對齊這裡。>

## Notes

<domain；每個 session 都該查閱的 skills；這次效力的常設偏好>

## Decisions so far

<!-- 這是 index——每張已關閉 ticket 一行：足以判斷相關性，然後 zoom 進 link 看 ticket 保存的細節 -->

- [<已關閉 ticket 的標題>](link) — <答案的一行 gist>

## Not yet specified

<!-- 見「Fog of war」：已在範圍內但還無法立 ticket 的迷霧；隨 frontier 推進而畢業 -->

## Out of scope

<!-- 見「Out of scope」：判定超出目的地範圍的工作；已關閉，永不畢業 -->
```

### Tickets

每張 ticket 都是地圖的一個 **child issue**；tracker 的 issue id 就是它的身分。它的內文是問題，大小應能裝進一個 100K token 的 agent session：

```markdown
## Question

<這張 ticket 要解決的決策或調查>
```

每張 ticket 都帶一個 `wayfinder:<type>` 標籤——`research`、`prototype`、`grilling`、`task` 其中之一（見 [Ticket Types](#ticket-types)）。

一個 session 在做任何工作**之前**，先透過把 ticket assign 給推進這張地圖的 developer 來**claim** 它，讓並行的 sessions 能跳過它。那個 assignee *就是* claim：open 且 unassigned 的 ticket 就是 unclaimed。

Blocking 使用 tracker 的 **native** dependency 關係——這很關鍵，因為它會在 tracker 自己的 UI 中把 frontier *視覺化*，讓人不用打開地圖就能看到哪些可以領取。只有當 tracker 缺乏 native blocking 時，才 fallback 到內文慣例。當阻擋一張 ticket 的每一張都已關閉，它就是 **unblocked**；**frontier** 是 open、unblocked、unclaimed 的 child tickets——也就是已知的邊界。

答案不放在內文裡——它會在解決時記錄下來（見〔處理地圖〕(#work-through-the-map)）。解決一張 ticket 時建立的 assets 用連結掛在 issue 上，不要貼進去。

## Ticket Types

每張 ticket 不是 **HITL**——human in the loop，和一個為自己發聲的人一起處理——就是 **AFK**，由 agent 單獨驅動。HITL ticket 只能透過那次真人互動來解決；agent 絕不能替人類那一側發言（一個自問自答的 grilling agent 就是破壞了這一點）。

- **Research**（AFK）：閱讀文件、第三方 API 或 knowledge base 之類的本地資源，浮現某個決策正在等待的事實。由 `/research` **subagent** 解決。當需要目前工作目錄之外的知識時使用。
- **Prototype**（HITL）：用便宜、粗糙、具體、可反應的 artifact 提高討論的 fidelity——outline、粗略草案、stub，或透過 /prototype skill 寫的 UI／邏輯程式碼。把 prototype 連結為 asset。當關鍵問題是「看起來該長怎樣」或「行為該怎樣」時使用。
- **Grilling**（HITL）：對話。這是預設情況。永遠呼叫 /grilling 和 /domain-modeling skills。
- **Task**（HITL 或 AFK）：在能做出*決策*之前必須完成的人工作業——沒有東西要決定、原型化或研究，但討論會卡住直到它完成。例如：註冊某個服務以便評估它的 API、provisioning access、搬動資料以便看清它的形狀。這是唯一*動手做*而非*決定*的類型——它靠解除某個決策的阻擋來取得存在的理由，而不是靠交付目的地本身。Agent 能自己驅動的就自己驅動（AFK）；否則交給人類一張精確的 checklist（HITL）。工作完成時 resolved；answer 記錄做了什麼，以及後續 tickets 依賴的事實（credentials 位置、新的 URL、row counts）。

## Fog of war

地圖*刻意*不完整：不要繪製你還看不見的東西。在 live tickets 之外，是 **fog of war（戰爭迷霧）**——你能感覺到即將到來、但還無法釘住的決策與調查的模糊視野，因為它們掛在仍未解決的問題上。解決一張 ticket 會清除它前方的迷霧，把現在能具體化的東西畢業成新的 tickets——一次一個，直到通往目的地的路清楚、沒有 tickets 剩下。

地圖的 **Not yet specified** 段落是寫下那個模糊視野的地方：疑似的問題、之後要重訪的區域。依目前視野允許的程度，鬆散或完整地寫下都可以；它同時也是給協作者看的路標，讓人知道這件事正朝哪裡走。

**迷霧還是 ticket？** 判斷標準是你現在能不能精確表述這個問題——*而不是*你現在能不能回答它。

- **該立 ticket**：問題已經 sharp——即使它被 blocked、你現在還動不了它。
- **該留在 Not yet specified**：你還無法把它說得那麼 sharp。不要把迷霧預先切成 ticket 大小的碎片：它比一張 ticket 粗，一塊迷霧在 frontier 抵達時，可能畢業成好幾張 tickets，也可能一張都沒有。

**Not yet specified** 不包含已經決定的東西（Decisions so far）、已經是 live ticket 的東西，以及超出範圍的東西（下一節）。

## Out of scope

迷霧只會朝著目的地方向累積。目的地決定了範圍，所以超出它的工作就是 **超出範圍（out of scope）**——它不是迷霧，也不屬於 **Not yet specified**。它在地圖上有自己的 **Out of scope** 段落：你已經意識到、明確排除在*這次*效力之外的工作。落在這裡的判準是範圍，不是清晰度。

超出範圍的工作永遠不會畢業——frontier 止步於目的地——所以它只有在目的地被重新繪製時才會回來，而且是以一次全新的效力回來，不是接續舊的。

判定某件事超出範圍是一個劃定範圍的動作，不是路線上的一步。當一張既有的 ticket 結果發現落在目的地之外——不論是繪圖時範圍抓錯，還是被某次解決揭露出來——**把它 close 掉**（一張 closed 的 ticket 明確不在 frontier 上），並在 **Out of scope** 段落留一行：gist 加上為什麼超出範圍，連結那張 closed ticket。它不進 **Decisions so far**——那裡記錄的是實際走過的路線，劃定範圍的邊界不是路線上的一步。

## Invocation

兩種模式。無論哪種，**每個 session 永遠不要解決超過一張 ticket**——research ticket 例外。

### 繪製地圖

使用者帶著一個鬆散的想法呼叫。

1. **命名目的地。** 執行一場 `/grilling` 和 `/domain-modeling` session，釘住這張地圖要找路前往的是什麼——spec、決策或改動。目的地決定範圍，所以要先定下來。
2. **繪製 frontier。** 再 grill 一次，這次**廣度優先**：在整個空間裡展開，而不是深挖某一條 thread，浮現出 open 的決策，以及現在就能踏出的第一步。**如果這一步沒有浮出任何迷霧**——通往目的地的路已經清楚，整趟旅程小到一個 session 就裝得下——你不需要地圖。停下來，問使用者接下來想怎麼進行。
3. **建立地圖**（標籤 `wayfinder:map`）：填好 Destination 和 Notes，Decisions-so-far 留空，把迷霧勾勒進 **Not yet specified**。
4. **建立現在就能具體化的 tickets**，作為地圖的 child issues——然後在**第二輪**接上 blocking edges（issues 要先有 id 才能互相引用）。接線會把它們分成 frontier 和 blocked；任何你還無法具體化的東西留在迷霧裡——也就是 **Not yet specified** 段落。
5. **啟動 research subagents。** 對每一張你剛建立的 `research` ticket，各自啟動一個 `/research` subagent 平行解決它，把發現記錄到一個可拋棄的 `research/<name>` 分支，並在 ticket 上留一個指向該分支的 context pointer。
6. 停下來——繪製地圖是一個 session 的工作；它不會親手解決任何 ticket。

### 處理地圖

使用者帶著一張地圖（URL 或編號）呼叫。Ticket 是**可選**的——沒有指定時，是你選下一個決策，不是使用者選。

1. 載入**地圖**——低解析度的視圖，不是每張 ticket 的內文。
2. 選一張 ticket。如果使用者指定了，就用那張；否則依序取第一張 frontier ticket。**Claim 它**：任何工作之前先 assign 給自己。
3. 解決它——**依需要 zoom**：按需取得任何相關或已關閉 ticket 的完整內文；呼叫 `## Notes` 區塊點名的 skills。不確定時，用 `/grilling` 和 `/domain-modeling`。
4. 記錄這次解決：把答案貼成一則 **resolution comment**，**close** 這個 issue，並在地圖的 Decisions-so-far **附上一個 context pointer**。
5. 加入新浮現的 tickets（先建立、再接線）；把答案讓其可具體化的迷霧畢業，並把每塊畢業的迷霧從 **Not yet specified** 清掉，讓它只存在於它的新 ticket 裡。如果答案揭露某張 ticket——這一張或另一張——落在目的地之外，就**判它超出範圍**，而不是把它當成路線的一部分解決掉。如果這個決策讓地圖的其他部分失效，就更新或刪除那些 tickets。

使用者可能會平行執行 unblocked 的 tickets，所以要預期其他 sessions 正在同時編輯這個 tracker。
