---
name: domain-modeling
description: "建立並精煉專案的領域模型。在討論程式碼庫術語、撰寫或編輯 GLOSSARY.md，或記錄或編輯 ADR 時使用。"
---

<a id="domain-modeling"></a>

# 領域建模

在設計過程中，積極建立並精煉專案的領域模型。這是一項*主動*的紀律：挑戰術語、構思邊界情境，並在概念成形的那一刻立即撰寫詞彙表與決策記錄。（單純*閱讀* `GLOSSARY.md` 以獲取詞彙並不屬於此技能：那是任何技能都能做到的一行習慣。此技能適用於你正在變更模型，而非僅消費模型的情況。）

<a id="file-structure"></a>

## 檔案結構

大多數儲存庫只有一個上下文：

```
/
├── GLOSSARY.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

如果根目錄存在 `GLOSSARY-MAP.md`，則該儲存庫有多個上下文。地圖指向每個上下文的存放位置：

```
/
├── GLOSSARY-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── GLOSSARY.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── GLOSSARY.md
│       └── docs/adr/
```

延遲建立檔案：僅在你有內容要寫入時才建立。如果 `GLOSSARY.md` 不存在，請在第一個術語解決時建立它。如果 `docs/adr/` 不存在，請在需要第一個 ADR 時建立它。

<a id="during-the-session"></a>

## 會話期間

<a id="challenge-against-the-glossary"></a>

### 與詞彙表進行對照挑戰

當使用者使用的術語與 `GLOSSARY.md` 中現有的語言衝突時，立即指出。「你的詞彙表將『取消』定義為 X，但你似乎指的是 Y。到底是哪一個？」

<a id="sharpen-fuzzy-language"></a>

### 精煉模糊的語言

當使用者使用模糊或過度載入的術語時，提出精確的標準術語。「你說的是『帳戶』：你指的是 Customer 還是 User？這些是不同的東西。」

<a id="discuss-concrete-scenarios"></a>

### 討論具體情境

當討論領域關係時，使用具體情境進行壓力測試。構思能探查邊界案例並迫使使用者精確界定概念之間邊界的情境。

<a id="cross-reference-with-code"></a>

### 與程式碼交叉參考

當使用者說明某項功能如何運作時，檢查程式碼是否一致。如果你發現矛盾，請提出來：「你的程式碼會取消整個 Order，但你剛才說部分取消是可能的。哪一個是對的？」

<a id="update-glossarymd-inline"></a>

### 即時更新 GLOSSARY.md

當術語解決時，立即更新 `GLOSSARY.md`。不要批次處理這些更新：在發生時即時記錄。使用 [GLOSSARY-FORMAT.md](./GLOSSARY-FORMAT.md) 中的格式。

`GLOSSARY.md` 應完全不含實作細節。不要將 `GLOSSARY.md` 視為規格、草稿本或實作決策的儲存庫。它是一個詞彙表，且僅限於此。

<a id="offer-adrs-sparingly"></a>

### 謹慎提供 ADR

僅當以下三點全部為真時，才提供建立 ADR 的選項：

1. **難以反轉**：稍後改變主意的代價是顯著的
2. **缺乏上下文時令人驚訝**：未來的讀者會疑惑「他們為什麼這樣做？」
3. **真實權衡的結果**：存在真正的替代方案，且你基於特定原因選擇了其中一個

如果缺少三點中的任何一點，請跳過 ADR。使用 [ADR-FORMAT.md](./ADR-FORMAT.md) 中的格式。
