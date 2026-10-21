<a id="learning-record-format"></a>

# 學習記錄格式

學習記錄存放於 `./learning-records/`，並使用連續編號：`0001-slug.md`、`0002-slug.md` 等。請延遲建立該目錄：僅在寫入第一筆記錄時才建立。

它們是教學領域中 ADR 的對應物：用於記錄非顯而易見的教訓、關鍵洞察，以及已陳述的先備知識，這些內容將引導未來的課程。它們用於計算最近發展區。

<a id="template"></a>

## 範本

```md
# {Short title of what was learned or established}

{1-3 sentences: what was learned (or what prior knowledge was established), and why it matters for future sessions.}
```

這就是完整的格式。學習記錄可以只是一個段落。其價值在於記錄「這現在已被知曉」以及「為什麼這會改變接下來要教什麼」，而非在於填寫各個章節。

<a id="optional-sections"></a>

## 選填章節

僅在這些專案能增添真正價值時才包含它們。大多數記錄不需要這些專案。

- **Status** frontmatter (`active | superseded by LR-NNNN`)：當先前的理解被證明是錯誤的並被取代時，此專案很有用。
- **Evidence**：使用者如何展現其理解（例如回答了一個問題、完成了一個練習、引用了先前經驗）。當該宣告可能需要重新檢視時，此專案很有用。
- **Implications**：這為未來課程解鎖了什麼，或排除了什麼。當其非顯而易見時，值得記錄。

<a id="numbering"></a>

## 編號

掃描 `./learning-records/` 以找到最高的現有編號，然後加一。

<a id="when-to-write-a-learning-record"></a>

## 何時撰寫學習記錄

當以下任一情況為真時，請撰寫一筆記錄：

1. **使用者展現了對某件非平凡事物的真正理解**：不僅是接觸過，而是有證據顯示他們能正確使用該概念。這為接下來要教什麼設定了新的基準。
2. **使用者披露了先備知識**：「我已經知道 X。」請記錄下來，以便未來課程不會重複教導。同時記錄其所聲稱的_深度_。
3. **一個誤解被糾正了**：使用者先前相信某件錯誤的事情，現在明白了原因。這些具有高價值：它們能預測未來在相關主題上可能遇到的障礙。
4. **任務因學習而發生轉變**：使用者發現自己關心的事情與原本以為的不同。請交叉連結至 [[MISSION.md]] 並更新它。

<a id="what-does-_not_-qualify"></a>

### 什麼_不_符合資格

- 僅被涵蓋過的內容。涵蓋不等於學習。請等待證據。
- 任何已在 [[GLOSSARY.md]] 中簡短記錄為術語定義的內容。請勿重複。
- 逐節的活動紀錄。學習記錄不是日記：它們是決策級別的洞察。

<a id="supersession"></a>

## 取代

當後續記錄與先前記錄矛盾時（使用者的理解加深或得到糾正），請將舊記錄標記為 `Status: superseded by LR-NNNN`，而非刪除它。理解如何演變的歷史本身也是有用的訊號。
