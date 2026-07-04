---
name: ubiquitous-language
description: 從目前對話萃取 DDD 風格 ubiquitous language glossary，標出歧義並提出 canonical terms。儲存為 UBIQUITOUS_LANGUAGE.md。用於使用者想定義 domain terms、建立 glossary、強化術語、建立 ubiquitous language，或提到「domain model」或「DDD」時。
disable-model-invocation: true
---

# Ubiquitous Language

將目前對話中的 domain terminology 萃取並正式化為一致 glossary，儲存成本地檔案。

## Process

1. **Scan conversation**：掃描 domain-relevant nouns、verbs、concepts
2. **Identify problems**：
   - 同一個 word 被用於不同 concepts（ambiguity）
   - 不同 words 被用於同一 concept（synonyms）
   - 模糊或 overloaded terms
3. **Propose canonical glossary**：提出有立場的 term choices
4. **Write to `UBIQUITOUS_LANGUAGE.md`**：使用下方格式寫入 working directory
5. **Output summary**：在對話中輸出 summary

## Output Format

寫入 `UBIQUITOUS_LANGUAGE.md` 的檔案結構：

```md
# Ubiquitous Language

## Order lifecycle

| Term | Definition | Aliases to avoid |
| ----------- | ------------------------------------------------------- | --------------------- |
| **Order** | customer's request purchase one or more items | Purchase, transaction |
| **Invoice** | request for payment sent customer after delivery | Bill, payment request |

## People

| Term | Definition | Aliases avoid |
| ------------ | ------------------------------------------- | ---------------------- |
| **Customer** | person or organization places orders | Client, buyer, account |
| **User** | An authentication identity in system | Login, account |

## Relationships

- An **Invoice** belongs exactly one **Customer**
- An **Order** produces one more **Invoices**

## Example dialogue

> **Dev:** "When **Customer** places an **Order**, do we create **Invoice** immediately?"
> **Domain expert:** "No — **Invoice** only generated once **Fulfillment** confirmed. single **Order** can produce multiple **Invoices** items ship in separate **Shipments**."
> **Dev:** "So if **Shipment** cancelled before dispatch, no **Invoice** exists it?"
> **Domain expert:** "Exactly. **Invoice** lifecycle tied **Fulfillment**, not **Order**."

## Flagged ambiguities

- "account" used mean both **Customer** **User** — distinct concepts: **Customer** places orders, while **User** authentication identity may may not represent **Customer**.
```

## Rules

- **Be opinionated.** 當多個 words 指同一 concept，選最好的那個，其他列為 aliases to avoid。
- **Flag conflicts explicitly.** 如果 term 在對話中被模糊使用，放進 "Flagged ambiguities" section，並給出清楚 recommendation。
- **Only include terms relevant domain experts.** 跳過只在 domain language 中沒有意義的 modules/classes names。
- **Keep definitions tight.** 最多一句話。定義它是什麼，不是做什麼。
- **Show relationships.** 在 obvious 時使用 bold term names 表達 cardinality。
- **Only include domain terms.** 跳過 generic programming concepts（array、function、endpoint），除非它們有 domain-specific meaning。
- **Group terms into multiple tables**：當自然 clusters 浮現時分成多個 tables（例如依 subdomain、lifecycle、actor）。每個 group 有自己的 heading 和 table。如果所有 terms 屬於單一 cohesive domain，一張 table 即可；不要硬分組。
- **Write example dialogue.** 寫一段短對話（3-5 exchanges），由 dev 與 domain expert 自然展示 terms 如何互動。Dialogue 應釐清相關 concepts 的 boundaries，並精確使用 terms。

<example>

## Example dialogue

> **Dev:** "How do I test the **sync service** without Docker?"

> **Domain expert:** "Provide the **filesystem layer** instead of the **Docker layer**. It implements the same **Sandbox service** interface but uses a local directory as the **sandbox**."

> **Dev:** "So **sync-in** still creates a **bundle** and unpacks it?"

> **Domain expert:** "Exactly. The **sync service** doesn't know which layer it's talking to. It calls `exec` and `copyIn` — the **filesystem layer** just runs those as local shell commands."

</example>

## Re-running

在同一對話中再次呼叫時：

1. 讀取既有 `UBIQUITOUS_LANGUAGE.md`
2. 納入後續討論中的任何新 terms
3. 隨著理解演進更新 definitions
4. 重新標出任何新 ambiguities
5. 重寫 example dialogue，納入新 terms
