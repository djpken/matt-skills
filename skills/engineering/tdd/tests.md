<a id="good-and-bad-tests"></a>

# 良好與不良的測試

<a id="good-tests"></a>

## 良好的測試

**整合式**：透過真實介面進行測試，而非模擬內部元件。

```typescript
// GOOD: Tests observable behavior
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

特性：

- 測試使用者或呼叫者關心的行為
- 僅使用公開 API
- 在內部重構後仍能透過
- 描述「是什麼」，而非「如何做到」
- 每個測試包含一個邏輯斷言

<a id="bad-tests"></a>

## 不良的測試

**實作細節測試**：與內部結構緊密耦合。

```typescript
// BAD: Tests implementation details
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

警示訊號：

- 模擬內部協作者
- 測試私有方法
- 斷言呼叫次數或順序
- 在行為未變化的情況下，重構會導致測試失敗
- 測試名稱描述「如何做到」而非「是什麼」
- 透過外部手段驗證，而非透過介面

```typescript
// BAD: Bypasses interface to verify
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// GOOD: Verifies through interface
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```

**同義反覆測試**：預期值重述了實作，因此測試天生就會透過。

```typescript
// BAD: Expected value is recomputed the way the code computes it
test("calculateTotal sums line items", () => {
  const items = [{ price: 10 }, { price: 5 }];
  const expected = items.reduce((sum, i) => sum + i.price, 0);
  expect(calculateTotal(items)).toBe(expected);
});

// GOOD: Expected value is an independent, known literal
test("calculateTotal sums line items", () => {
  expect(calculateTotal([{ price: 10 }, { price: 5 }])).toBe(15);
});
```
