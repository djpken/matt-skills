---
name: migrate-to-shoehorn
description: "將測試檔案從 `as` 型別斷言遷移至 @total-typescript/shoehorn。當使用者提到 shoehorn、希望取代測試中的 `as`，或需要部分測試資料時使用。"
---

<a id="migrate-to-shoehorn"></a>

# 遷移至 Shoehorn

<a id="why-shoehorn"></a>

## 為什麼使用 shoehorn？

`shoehorn` 讓您在測試中傳遞部分資料，同時保持 TypeScript 的型別檢查正常運作。它以型別安全的替代方案取代 `as` 斷言。

**僅限測試程式碼。** 切勿在生產環境程式碼中使用 shoehorn。

測試中使用 `as` 的問題：

- 受過訓練不使用它
- 必須手動指定目標型別
- 雙重 as（`as unknown as Type`）用於故意錯誤的資料

<a id="install"></a>

## 安裝

```bash
npm i @total-typescript/shoehorn
```

<a id="migration-patterns"></a>

## 遷移模式

<a id="large-objects-with-few-needed-properties"></a>

### 大型物件中僅需少數屬性

之前：

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...20 more properties
};

it("gets user by id", () => {
  // Only care about body.id but must fake entire Request
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...fake all 20 properties
  });
});
```

之後：

```ts
import { fromPartial } from "@total-typescript/shoehorn";

it("gets user by id", () => {
  getUser(
    fromPartial({
      body: { id: "123" },
    }),
  );
});
```

### `as Type` → `fromPartial()`

之前：

```ts
getUser({ body: { id: "123" } } as Request);
```

之後：

```ts
import { fromPartial } from "@total-typescript/shoehorn";

getUser(fromPartial({ body: { id: "123" } }));
```

### `as unknown as Type` → `fromAny()`

之前：

```ts
getUser({ body: { id: 123 } } as unknown as Request); // wrong type on purpose
```

之後：

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

<a id="when-to-use-each"></a>

## 何時使用各函式

| 函式        | 使用情境                                           |
| --------------- | -------------------------------------------------- |
| `fromPartial()` | 傳遞仍能透過型別檢查的部分資料           |
| `fromAny()`     | 傳遞故意錯誤的資料（保留自動補全功能） |
| `fromExact()`   | 強制使用完整物件（稍後可與 fromPartial 互換）    |

<a id="workflow"></a>

## 工作流程

1. **收集需求** - 詢問使用者：
   - 哪些測試檔案中的 `as` 斷言造成了問題？
   - 他們是否處理大型物件，且僅部分屬性重要？
   - 他們是否需要傳遞故意錯誤的資料以進行錯誤測試？

2. **安裝並遷移**：
   - [ ] 安裝：`npm i @total-typescript/shoehorn`
   - [ ] 尋找包含 `as` 斷言的測試檔案：`grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] 將 `as Type` 取代為 `fromPartial()`
   - [ ] 將 `as unknown as Type` 取代為 `fromAny()`
   - [ ] 從 `@total-typescript/shoehorn` 新增匯入
   - [ ] 執行型別檢查以驗證
