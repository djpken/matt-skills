---
name: domain-modeling
description: 建立並精煉專案的領域模型。用於：使用者想確立領域術語或通用語言、記錄架構決策，或其他 skill 需要維護領域模型時。
---

# Domain Modeling（領域建模）

在設計時主動建立並精煉專案的領域模型。這是*主動*的紀律——挑戰術語、發明邊界案例情境、並在決策結晶的瞬間把術語表和決策寫下來。（只是*閱讀* `CONTEXT.md` 取得詞彙不是這個 skill——那是任何 skill 都能做的一行習慣。這個 skill 是用於你正在*改變*模型，而不只是消費它時。）

## 檔案結構

大多數 repo 只有一個 context：

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

如果根目錄存在 `CONTEXT-MAP.md`，表示 repo 有多個 context。這個 map 指向每個 context 所在的位置：

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← 系統層面的決策
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← context 特定的決策
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

懶惰地建立檔案——只在有東西要寫時才建立。如果不存在 `CONTEXT.md`，在第一個術語解決時建立它。如果不存在 `docs/adr/`，在第一個 ADR 需要時建立它。

## 在 session 中

### 對照術語表挑戰

當使用者使用與 `CONTEXT.md` 中現有語言衝突的術語時，立即指出來。「你的術語表將『cancellation』定義為 X，但你似乎的意思是 Y——哪個才對？」

### 磨練模糊語言

當使用者使用含糊或過載的術語時，提出一個精確的規範術語。「你說的是『account』——你的意思是 Customer 還是 User？這是不同的東西。」

### 討論具體情境

在討論領域關係時，用具體情境對它們進行壓力測試。發明探測邊界案例的情境，迫使使用者對概念之間的邊界精確。

### 與程式碼交叉引用

當使用者說明某些東西的運作方式時，檢查程式碼是否同意。如果你發現矛盾，浮出它：「你的程式碼取消整個訂單，但你剛才說部分取消是可能的——哪個才對？」

### 即時更新 CONTEXT.md

當一個術語被解決時，立刻更新 `CONTEXT.md`。不要把這些批量累積——在發生的當下捕獲它們。使用 [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md) 中的格式。

`CONTEXT.md` 應該完全沒有實作細節。不要把 `CONTEXT.md` 當作規格書、草稿或實作決策的儲存庫。它只是術語表，僅此而已。

### 謹慎提供 ADR

只有在以下三個條件全部成立時才提供建立 ADR：

1. **難以逆轉** — 事後改變想法的代價是有意義的
2. **沒有背景會令人驚訝** — 未來的讀者會納悶「他們為什麼這樣做？」
3. **真實權衡的結果** — 有真正的替代方案，你為特定原因選擇了一個

如果三個中缺少任何一個，跳過 ADR。使用 [ADR-FORMAT.md](./ADR-FORMAT.md) 中的格式。
