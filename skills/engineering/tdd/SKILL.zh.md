---
name: tdd
description: 測試驅動開發。用於：使用者想先寫測試再實作功能或修 bug、提到「紅綠重構」，或想要整合測試時。
---

# Test-Driven Development（測試驅動開發）

TDD 是 red → green 迴路。這個 skill 是讓該迴路產生值得保留的測試的參考：什麼是好測試、測試放在哪裡、反模式，以及迴路規則。每一節都適用於每次 cycle：在迴路之前和迴路中查閱，不要等到之後。

探索 codebase 時，讀取 `CONTEXT.md`（如果存在），讓測試名稱和介面詞彙符合專案的 domain language，並遵守你正在觸碰區域的 ADR。

## 什麼是好測試

測試透過 public interfaces 驗證 behavior，而不是 implementation details。程式碼可以完全改變；測試不應該。好測試讀起來像 specification：「user can checkout with valid cart」精確告訴你存在什麼 capability；它能在 refactor 後存活，因為它不關心 internal structure。

範例見 [tests.md](tests.md)，mocking guidelines 見 [mocking.md](mocking.md)。

## Seams — 測試放在哪裡

**Seam** 是你測試的 public boundary：你能觀察 behavior、但不伸手進內部的 interface。測試住在 seams 上，永遠不要針對 internals。

**只測預先同意的 seams。** 寫任何測試前，先寫下要測的 seams，並與使用者確認。未確認的 seam 不寫測試。你無法測試所有東西；預先同意 seams，才能把測試精力放到 critical paths 和 complex logic，而不是每個 edge case。

問：「What's the public interface, and which seams should we test?」

## Anti-patterns

- **Implementation-coupled**：mock internal collaborators、測 private methods，或透過 side channel 驗證（查 database 而不是使用 interface）。徵兆：refactor 時測試壞掉，但 behavior 沒變。
- **Tautological**：assertion 用和程式碼相同的方式重新計算 expected value（`expect(add(a, b)).toBe(a + b)`、用同樣方式手動產生的 snapshot、constant asserted equal to itself），所以它天生會過，永遠不會和程式碼不同意。Expected values 必須來自獨立 truth source：known-good literal、worked example、spec。
- **Horizontal slicing**：先寫所有 tests，再寫所有 implementation。Bulk tests 驗證的是*想像中的* behavior：你測到的是東西的 shape，而不是 user-facing behavior；測試對真實變更變得不敏感，且在理解 implementation 前就承諾 test structure。改用 **vertical slices**：one test → one implementation → repeat，每個 test 都是一顆 **tracer bullet**，回應上一個 cycle 教你的東西。

## Rules of the loop

- **Red before green.** 先寫 failing test，再寫剛好足以通過的 code。不要預測 future tests，也不要加入 speculative features。
- **One slice at a time.** 每個 cycle 一個 seam、一個 test、一個 minimal implementation。
- **Refactoring is not part of the loop.** Refactoring 屬於 review stage（見 `code-review` skill），不是 red → green implementation cycle 的一部分。
