<a id="glossarymd-format"></a>

# GLOSSARY.md 格式

<a id="structure"></a>

## 結構

```md
# {Context Name}

{One or two sentence description of what this context is and why it exists.}

## Language

**Order**:
{A one or two sentence description of the term}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account
```

<a id="rules"></a>

## 規則

- **要有明確立場。** 當同一概念有多個詞彙時，選取最合適的一個，並將其他詞彙列於 `_Avoid_` 之下。
- **定義要精簡。** 最多一到兩句話。定義它「是什麼」，而不是它「做什麼」。
- **僅納入特定於此專案脈絡的詞彙。** 即使專案大量使用，一般程式設計概念（逾時、錯誤型別、工具模式）也不應納入。在新增詞彙前，請自問：這是此脈絡獨有的概念，還是一般程式設計概念？只有前者才屬於此處。
- **在自然群組出現時，將詞彙歸類於副標題之下。** 如果所有詞彙都屬於單一連貫領域，使用平鋪清單即可。

<a id="single-vs-multi-context-repos"></a>

## 單一脈絡與多脈絡儲存庫

**單一脈絡（大多數儲存庫）：** 在儲存庫根目錄放置一個 `GLOSSARY.md`。

**多個脈絡：** 在儲存庫根目錄的 `GLOSSARY-MAP.md` 中列出各脈絡、其位置以及它們之間的關係：

```md
# Glossary Map

## Contexts

- [Ordering](./src/ordering/GLOSSARY.md): receives and tracks customer orders
- [Billing](./src/billing/GLOSSARY.md): generates invoices and processes payments
- [Fulfillment](./src/fulfillment/GLOSSARY.md): manages warehouse picking and shipping

## Relationships

- **Ordering → Fulfillment**: Ordering emits `OrderPlaced` events; Fulfillment consumes them to start picking
- **Fulfillment → Billing**: Fulfillment emits `ShipmentDispatched` events; Billing consumes them to generate invoices
- **Ordering ↔ Billing**: Shared types for `CustomerId` and `Money`
```

此 skill 會推斷適用哪種結構：

- 如果 `GLOSSARY-MAP.md` 存在，讀取它以尋找脈絡
- 如果只有根目錄的 `GLOSSARY.md` 存在，則為單一脈絡
- 如果兩者都不存在，在解析第一個詞彙時，延遲建立根目錄的 `GLOSSARY.md`

當存在多個脈絡時，推斷目前主題與哪一個相關。如果不清楚，請詢問。
