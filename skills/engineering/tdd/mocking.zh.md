<a id="when-to-mock"></a>

# 何時使用 Mock

僅在**系統邊界**使用 Mock：

- 外部 API（付款、電子郵件等）
- 資料庫（有時，優先使用測試資料庫）
- 時間/隨機性
- 檔案系統（有時）

不要 Mock：

- 您自己的類別/模組
- 內部協作者
- 任何您能控制的事物

<a id="designing-for-mockability"></a>

## 為可 Mock 性進行設計

在系統邊界，設計容易 Mock 的介面：

**1. 使用相依性注入**

將外部相依性傳入，而非在內部建立：

```typescript
// Easy to mock
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// Hard to mock
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. 優先使用 SDK 風格介面，而非通用擷取器**

為每個外部操作建立特定函式，而非使用一個包含條件邏輯的通用函式：

```typescript
// GOOD: Each function is independently mockable
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// BAD: Mocking requires conditional logic inside the mock
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

SDK 方法意味著：
- 每個 Mock 回傳一個特定的結構
- 測試設定中沒有條件邏輯
- 更容易看出測試執行了哪些端點
- 每個端點都有型別安全性
