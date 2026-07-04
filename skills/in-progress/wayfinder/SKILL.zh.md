---
name: wayfinder
description: 規劃巨大工作——超過一個 agent session 能承載的範圍——在 issue tracker 上建立共享調查票券地圖，逐一解決 tickets，直到通往目標的路徑清楚為止。
---

鬆散想法出現了：太大，單一 agent session 裝不下，而且被 fog 包住；從這裡到計畫的路線還看不見。這個 skill 會在 repo 的 issue tracker 上繪製**共享 map**，然後一次處理一張 ticket。Map 與 domain 無關：engineering work、course content，任何符合這個形狀的工作都可以。

## Refer by name

每個 map ticket 都是 issue，所以它有**名字**，也就是 title。在所有人會讀到的地方——敘述、map 的 Decisions-so-far——都用名字引用，不要只用裸 id、number 或 slug。滿牆 `#42, #43, #44` 無法閱讀；名字一眼就能懂。Id 和 URL 不會消失，名字會包住 link，但它們只放在名字*裡面*，不要取代名字。

## Map

Map 是 repo issue tracker 上的一個 issue，標籤是 `wayfinder:map`，它是 canonical artifact。Tickets 是 map 的 child issues。

Map 是 **index**，不是 store。它列出已做決策，指向保存細節的 tickets；decision 只存在於一個地方：ticket。因此 map 永遠不重述決策，只給 gist 和 link。

**Map、child tickets、blocking、frontier queries 實際存在的位置取決於 tracker。** 查閱 `docs/agents/issue-tracker.md` 的 "Wayfinding operations" section，看這個 repo 如何表達它們。如果文件不存在，預設使用 local-markdown tracker。

### map body

整張 map 以低解析度呈現，每個 session 載入一次。Open tickets **不**列在 map body；它們是 open child issues，用 query 找。

```markdown
## Notes

<domain; skills every session should consult; standing preferences effort>

## Decisions so far

<!-- index — one line per closed ticket: enough judge relevance, then zoom link detail ticket holds -->

- <closed ticket title> — <ticket link> — <one-line gist answer>

## Fog

<!-- see "Fog of war" belongs here -->
```

### Tickets

每張 ticket 都是 map 的 **child issue**；tracker 的 issue id 是它的 identity。Body 是問題，大小應能在一個 100K token agent session 內完成：

```markdown
## Question

<the decision or investigation ticket resolves>
```

每張 ticket 都帶 `wayfinder:<type>` label：`research`、`prototype`、`grilling`、`task` 其中之一（見 [Ticket Types](#ticket-types)）。

Session 在任何工作前，**先 claim** ticket：把 ticket assign 給正在推進 map 的 developer，讓並行 sessions 會跳過它。Assignee _就是_ claim：open 且 unassigned 的 ticket 就是 unclaimed。

Blocking 使用 tracker 的**原生** dependency relationship，這很重要，因為它會在 tracker 自己的 UI 中_視覺化_ frontier，讓人不用打開 map 就知道哪些可拿。只有當 tracker 沒有 native blocking 時，才 fallback 到 body convention。當所有 blocking tickets 都 closed，ticket 就 **unblocked**；**frontier** 是 open、unblocked、unclaimed 的 child issues，也就是已知邊界。

Answer 不放在 body；它在 resolution 時記錄（見 [Work through map](#work-through-map)）。解 ticket 時建立的 assets 連到 issue，不貼進去。

## Ticket Types

- **Research**：閱讀 documentation、third-party APIs 或 knowledge bases 等 local resources。產出 linked markdown summary asset。需要目前 working directory 外的知識時使用。
- **Prototype**：用便宜、粗糙、具體、可反應的 artifact 提高討論 fidelity，例如 outline、rough take、stub，或透過 /prototype skill 寫 UI/logic code。將 prototype 作為 asset 連結。當關鍵問題是 "how should it look" 或 "how should it behave" 時使用。
- **Grilling**：對話 agent。使用 /grilling 和 /domain-modeling skills。一次問一個問題。這是預設類型。
- **Task**：討論能前進前必須完成的 literal manual work；沒有東西要 decide、prototype 或 research。例：搬資料、註冊服務、provisioning access。Agent 盡量自動化；否則交給 human 精確 checklist。Work done 時 resolved；answer 記錄完成了什麼，以及後續 tickets 依賴的 facts（credentials location、new URLs、row counts）。

## Fog war

Map 會_刻意_不完整：不要繪製還看不見的東西。Tickets 之外是 fog：你知道之後會有一些 decisions / investigations，但還無法準確釘住，因為它們掛在仍未解的問題上。解決 ticket 會清除它前方的 fog，把現在能具體化的東西升級成 fresh tickets；一次一張，直到通往目標的路清楚且沒有 tickets 留下。

Map 的 **Fog** section 是寫下 dim view 的地方：疑似問題、稍後重訪的區域、正在延後的風險。依目前視野能允許的程度鬆散書寫；它也會成為 collaborators 閱讀時的路標。

**Fog or ticket?** 測試標準是你現在能否精確表述問題，而_不是_你現在能否回答。

- **Ticket when** 問題已經 sharp，即使它被 blocked、你還不能處理。
- **Fog when** 你還不能把它說 sharp。不要把 fog 預先切成 ticket-sized pieces；fog 比 ticket 更粗，一塊 fog 可能在 frontier 抵達時升級成多張 tickets，或一張都沒有。

Fog 只排除已經 decided 的東西（那是 Decisions so far）和已經 ticketed 的東西。

## Invocation

兩種模式。無論哪種，**每個 session 永遠不要解超過一張 ticket。**

### Chart map

使用者帶著鬆散想法呼叫。

1. 執行 `/grilling` 和 `/domain-modeling` session，浮出 open decisions。
2. **Create map**（label `wayfinder:map`）：填入 Notes、Decisions-so-far 留空、Fog sketch。
3. **Create tickets you can specify now** 作為 map 的 child issues，然後在**第二 pass** wire blocking edges（issues 需要 ids 才能互相 reference）。Wiring 會把它們分成 frontier / blocked；任何你還不能具體化的東西留在 Fog。
4. 停下來。Charting map 是一個 session 的工作；不要同時解 tickets。

### Work through map

使用者帶著 map（URL 或 number）呼叫。Ticket 是**可選**的；如果沒有指定，你選下一個 decision，而不是使用者選。

1. 載入 **map**：低解析度 view，不是每張 ticket body。
2. 選 ticket。如果使用者指定了，就用它；否則依序取第一張 frontier ticket。**Claim it**：任何工作前先 assign 給自己。
3. 解決它：**依需要 zoom**，按需取得 full body、任何 related 或 closed ticket；呼叫 `## Notes` block 命名的 skills。不確定時，用 `/grilling` 和 `/domain-modeling`。
4. 記錄 resolution：發布 answer **resolution comment**、**close** issue、在 map 的 Decisions-so-far **append context pointer**。
5. 加入 newly-surfaced tickets（create-then-wire）；把 answer 讓它變得可具體化的 fog 升級，並從 Fog 清除每個 graduated patch，讓它只存在於新的 ticket。如果 decision 讓 map 其他部分失效，更新或刪除 tickets。

使用者可能平行執行 unblocked tickets，所以預期其他 sessions 會同時編輯 tracker。
