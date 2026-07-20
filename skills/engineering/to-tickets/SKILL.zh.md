---
name: to-tickets
description: 將計畫、spec 或目前對話拆成一組 tracer-bullet tickets，每張都宣告自己的 blocking edges，並發布到已設定的 tracker——本地是每張 ticket 一個檔案，真正的 tracker 則用 native blocking links。
disable-model-invocation: true
---

# To Tickets

把計畫、spec 或對話拆成一組 **tickets**——tracer-bullet 垂直切片，每張都宣告會**阻擋（block）**它的 tickets。

Issue 追蹤器和 triage 標籤詞彙應該已提供給你——如果沒有，執行 `/setup-matt-pocock-skills`。

## 流程

### 1. 蒐集 context

從對話 context 中已有的東西開始。如果使用者傳入參考資料（spec 路徑、issue 編號或 URL）作為參數，取得它並讀取完整內文與留言。

### 2. 探索程式碼庫（可選）

如果你還沒有探索過程式碼庫，先探索一下以了解程式碼目前的狀態。Ticket 標題與描述應使用專案的領域術語表詞彙，並遵循你正在觸碰區域的 ADR。

尋找 prefactor 程式碼的機會，讓實作更容易。「先讓改動變容易，再做容易的改動。」

### 3. 草擬垂直切片

把工作拆成 **tracer bullet** tickets。

<vertical-slice-rules>

- 每個切片提供一條穿越每個層（schema、API、UI、測試）的窄但**完整**路徑——是垂直切片，**不是**單一層的水平切片
- 完成的切片可以獨立展示或驗證
- 每個切片的大小應能裝進單一個全新的 context 視窗
- 任何 prefactoring 都應該先完成

</vertical-slice-rules>

給每張 ticket 標上它的 **blocking edges**——必須先完成才能開始的其他 tickets。沒有 blocker 的 ticket 可以立即開始。

**大範圍重構是垂直切片的例外。** **Wide refactor** 是指單一機械式改動——重新命名欄位、改變共享符號的型別——但它的 **blast radius** 橫跨整個程式碼庫，一次編輯就會同時弄壞成千上百個呼叫點，沒有任何垂直切片能單獨落地為 green。不要硬塞進 tracer bullet；改用 **expand–contract** 排序。先 expand：在舊形式旁邊加上新形式，什麼都不會壞。接著依 blast radius 分批（依 package、依目錄）把呼叫點遷移過去，每一批各自是一張被 expand 阻擋的 ticket，讓 CI 在每批之間保持 green，因為舊形式仍然存在。最後 contract：等不再有任何呼叫者時，用一張被所有遷移批次阻擋的 ticket 刪除舊形式。當連各批次都無法單獨保持 green 時，仍維持這個順序，但讓它們共用一條 integration branch，全部阻擋最後一張 integrate-and-verify ticket——green 只在那裡才被承諾。

### 4. 詢問使用者

把提議的拆分以編號列表呈現。每張 ticket 顯示：

- **標題**：簡短描述性名稱
- **被阻擋於（Blocked by）**：哪些其他 tickets（若有）必須先完成
- **交付內容**：這張 ticket 讓什麼端對端行為可運作

問使用者：

- 顆粒度感覺對嗎？（太粗 / 太細）
- Blocking edges 正確嗎——每張 ticket 是否只依賴真正阻擋它的 tickets？
- 有沒有 tickets 該合併或再拆分？

反覆調整，直到使用者核准這個拆分。

### 5. 把 tickets 發布到已設定的 tracker

發布核准的 tickets。**怎麼發布**取決於 `/setup-matt-pocock-skills` 設定的 tracker——tickets 本身一樣，只有 blocking edges 的表達形式不同：

- **本地檔案** → 在 `.scratch/<feature-slug>/issues/<NN>-<slug>.md` 下每張 ticket 寫一個檔案，從 `01` 開始依相依順序編號（blocker 在前）。每個檔案的「Blocked by」列出它依賴的編號／標題。使用下方的單張 ticket 檔案模板——一張 ticket 一個檔案，絕不合併成單一檔案。
- **真正的 issue tracker（GitHub、Linear……）** → 依相依順序（blocker 在前）為每張 ticket 各發布一個 issue，讓每張 ticket 的 blocking edges 可以引用真實的識別碼。若 tracker 有 native 的 blocking／sub-issue 關係就用它；否則把每張 ticket 的「Blocked by」設成阻擋它的 issues。除非另有指示，套用 `ready-for-agent` triage 標籤——這些 tickets 天生就是 agent 可領取的。

處理 **frontier**：任何 blocker 都已完成的 ticket。對純線性鏈來說，就是由上到下。

不要 close 或修改任何 parent issue。

<local-ticket-template>

# <NN> — <Ticket 標題>

**要建構什麼：** 這張 ticket 從使用者角度讓什麼端對端行為可運作——不是逐層的實作清單。

**Blocked by：** 阻擋這張的 tickets 編號／標題，或「無——可以立即開始」。

**Status：** ready-for-agent

- [ ] 驗收標準 1
- [ ] 驗收標準 2

</local-ticket-template>

<issue-template>

## Parent

在 tracker 上對 parent issue 的引用（若來源是既有 issue，否則省略此段）。

## 要建構什麼

這張 ticket 從使用者角度讓什麼端對端行為可運作——不是逐層的實作清單。

## 驗收標準

- [ ] 標準 1
- [ ] 標準 2

## Blocked by

- 對每張阻擋它的 ticket 的引用，或「無——可以立即開始」。

</issue-template>

不論哪種形式，避免具體的檔案路徑或程式碼片段——它們很快就會過時。例外：如果原型產生了一個比散文更精確地編碼決策的片段（狀態機、reducer、schema、型別形狀），內嵌它並簡單注明它來自原型。修剪到決策豐富的部分——不是一個可運作的演示，只是重要的位元。

用 `/implement` 一次處理 frontier 的一張 ticket，每張之間清除 context。
