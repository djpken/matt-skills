---
name: migrate-to-shoehorn
description: 將測試檔案從 `as` 型別斷言遷移至 @total-typescript/shoehorn。用於：使用者提到 shoehorn、想在測試中取代 `as`，或需要部分測試資料時。
---

# Migrate to Shoehorn（遷移到 Shoehorn）

## 為什麼使用 shoehorn？

`shoehorn` 讓你在測試中傳遞部分資料，同時保持 TypeScript 的滿意。它用型別安全的替代方案取代 `as` 斷言。

**僅限測試程式碼。** 絕不在生產程式碼中使用 shoehorn。

在測試中使用 `as` 的問題：

- 被訓練不要使用它
- 必須手動指定目標型別
- 對於故意錯誤的資料需要雙重 as（`as unknown as Type`）

## 安裝

```bash
npm i @total-typescript/shoehorn
```

## 遷移模式

### 只需要幾個屬性的大型物件

之前：

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...20 個以上的屬性
};

it("gets user by id", () => {
  // 只關心 body.id 但必須偽造整個 Request
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...偽造所有 20 個屬性
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
getUser({ body: { id: 123 } } as unknown as Request); // 故意錯誤的型別
```

之後：

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

## 何時使用哪個

| 函式            | 使用案例                                     |
| --------------- | -------------------------------------------- |
| `fromPartial()` | 傳遞仍通過型別檢查的部分資料                 |
| `fromAny()`     | 傳遞故意錯誤的資料（保留自動補全）           |
| `fromExact()`   | 強制完整物件（之後可換回 fromPartial）       |

## 工作流程

1. **收集需求** — 詢問使用者：
   - 哪些測試檔案有造成問題的 `as` 斷言？
   - 他們是否在處理只有部分屬性重要的大型物件？
   - 他們是否需要為錯誤測試傳遞故意錯誤的資料？

2. **安裝並遷移**：
   - [ ] 安裝：`npm i @total-typescript/shoehorn`
   - [ ] 找到有 `as` 斷言的測試檔案：`grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] 將 `as Type` 替換為 `fromPartial()`
   - [ ] 將 `as unknown as Type` 替換為 `fromAny()`
   - [ ] 從 `@total-typescript/shoehorn` 添加匯入
   - [ ] 執行型別檢查以驗證
