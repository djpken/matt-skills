---
name: design-an-interface
description: 使用並行 sub-agents 為模組產生多個截然不同的介面設計。用於使用者想設計 API、探索介面選項、比較模組形狀，或提到「design it twice」時。
---

# Design an Interface

基於《A Philosophy of Software Design》中的「Design It Twice」：第一個想法通常不會是最好的。產生多個截然不同的 designs，然後比較。

## Workflow

### 1. Gather Requirements

設計前，先理解：

- [ ] 這個模組解決什麼問題？
- [ ] 呼叫者是誰？（其他模組、外部使用者、tests）
- [ ] 關鍵 operations 是什麼？
- [ ] 有哪些 constraints？（performance、compatibility、existing patterns）
- [ ] 什麼應該藏在內部，什麼應該暴露？

詢問：「What does this module need to do? Who will use it?」

### 2. Generate Designs (Parallel Sub-Agents)

使用 Task tool 同時 spawn 3+ sub-agents。每個都必須產出一個**截然不同**的方法。

```text
Prompt template for each sub-agent:

Design an interface for: [module description]

Requirements: [gathered requirements]

Constraints for this design: [assign a different constraint to each agent]
- Agent 1: "Minimize method count - aim for 1-3 methods max"
- Agent 2: "Maximize flexibility - support many use cases"
- Agent 3: "Optimize for the most common case"
- Agent 4: "Take inspiration from [specific paradigm/library]"

Output format:
1. Interface signature (types/methods)
2. Usage example (how caller uses it)
3. What this design hides internally
4. Trade-offs of this approach
```

### 3. Present Designs

每個 design 都展示：

1. **Interface signature**：types、methods、params
2. **Usage examples**：callers 在實務上如何使用
3. **What it hides**：保留在內部的 complexity

依序呈現 designs，讓使用者能先吸收每個 approach，再進入比較。

### 4. Compare Designs

展示所有 designs 後，依以下面向比較：

- **Interface simplicity**：methods 更少、params 更簡單
- **General-purpose vs specialized**：彈性 vs 聚焦
- **Implementation efficiency**：這個 shape 是否允許高效率 internals？
- **Depth**：小 interface 隱藏大量 complexity（好）vs 大 interface 搭配薄 implementation（壞）
- **Ease of correct use** vs **ease of misuse**

用 prose 討論 trade-offs，不要用 tables。標出 designs 分歧最大的地方。

### 5. Synthesize

最好的 design 常常結合多個 options 的 insights。詢問：

- "Which design best fits your primary use case?"
- "Any elements from other designs worth incorporating?"

## Evaluation Criteria

來自《A Philosophy of Software Design》：

**Interface simplicity**：methods 更少、params 更簡單 = 更容易學會並正確使用。

**General-purpose**：可不改動就處理未來 use cases。但小心過度泛化。

**Implementation efficiency**：interface shape 是否允許高效率 implementation？還是迫使 awkward internals？

**Depth**：小 interface 隱藏大量 complexity = deep module（好）。大 interface 搭配薄 implementation = shallow module（避免）。

## Anti-Patterns

- 不要讓 sub-agents 產出相似 designs；強制 radical difference
- 不要跳過比較；價值在 contrast
- 不要實作；這純粹討論 interface shape
- 不要依 implementation effort 評估
