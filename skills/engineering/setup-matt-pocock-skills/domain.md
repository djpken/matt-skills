<a id="domain-docs"></a>

# 領域檔案

工程技能在探索程式碼庫時，應如何使用此儲存庫的領域檔案。

<a id="before-exploring-read-these"></a>

## 在探索之前，請先閱讀以下內容

- 位於儲存庫根目錄的 **`GLOSSARY.md`**，或
- 若存在，位於儲存庫根目錄的 **`GLOSSARY-MAP.md`**：它指向每個上下文的一個 `GLOSSARY.md`。請閱讀與主題相關的每一項。
- **`docs/adr/`**：閱讀涉及您即將處理領域的 ADR。在多上下文儲存庫中，也請檢查 `src/<context>/docs/adr/` 以瞭解上下文範圍內的決策。

如果這些檔案中有任何一個不存在，請**靜默繼續**。不要標記其缺失，也不要建議事先建立它們。`/domain-modeling` 技能（透過 `/grill-with-docs` 和 `/improve-codebase-architecture` 到達）會在術語或決策實際解決時，按需建立它們。

<a id="file-structure"></a>

## 檔案結構

單一上下文儲存庫（大多數儲存庫）：

```
/
├── GLOSSARY.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多上下文儲存庫（根目錄存在 `GLOSSARY-MAP.md`）：

```
/
├── GLOSSARY-MAP.md
├── docs/adr/                          ← system-wide decisions
└── src/
    ├── ordering/
    │   ├── GLOSSARY.md
    │   └── docs/adr/                  ← context-specific decisions
    └── billing/
        ├── GLOSSARY.md
        └── docs/adr/
```

<a id="use-the-glossarys-vocabulary"></a>

## 使用詞彙表的詞彙

當您的輸出命名某個領域概念（例如在問題標題、重構提案、假設或測試名稱中）時，請使用 `GLOSSARY.md` 中定義的術語。不要偏離詞彙表明確避免的同義詞。

如果您需要的概念尚未包含在詞彙表中，這是一個訊號：要麼您正在發明專案未使用的語言（請重新考慮），要麼存在真正的缺口（請記錄下來以供 `/domain-modeling` 參考）。

<a id="flag-adr-conflicts"></a>

## 標記 ADR 衝突

如果您的輸出與現有的 ADR 相矛盾，請明確指出，而不是靜默覆蓋：

> _與 ADR-0007（事件溯源訂單）相矛盾，但值得重新開啟，因為……_
