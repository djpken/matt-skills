<a id="html-report-format"></a>

# HTML 報告格式

架構審查會以單一自包含的 HTML 檔案形式呈現，並儲存在 OS 暫存目錄中。Tailwind 和 Mermaid 皆來自 CDN。Mermaid 能可靠地處理圖形結構的圖表；手動建立的 div 和內嵌 SVG 則負責更具編輯風格的視覺效果（例如質量圖、橫截面圖）。請混合使用這兩者：不要事事依賴 Mermaid，否則看起來會過於泛泛。

<a id="scaffold"></a>

## 骨架

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Architecture review for {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* small custom layer for things Tailwind doesn't cover cleanly:
         dashed seam lines, hand-drawn-feeling arrow heads, etc. */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

<a id="header"></a>

## 標頭

儲存庫名稱、日期，以及簡潔的圖例：實線方框 = 模組，虛線 = 接縫，紅色箭頭 = 洩漏，粗黑方框 = 深層模組。不需要引言段落。直接進入候選專案。

<a id="candidate-card"></a>

## 候選專案卡片

圖表承擔主要說明功能。文字應簡潔、平實，並使用術語表（來自 `/codebase-design` skill）中的詞彙，不需繁複修飾。

每個候選專案為一個 `<article>`：

- **標題**：簡短，指出深化方向（例如「合併 Order 接收管線」）。
- **徽章列**：建議強度（`Strong` = 翡翠綠，`Worth exploring` = 琥珀色，`Speculative` = 石板色），外加一個依賴類別標籤（`in-process`、`local-substitutable`、`ports & adapters`、`mock`）。
- **檔案**：等寬字型清單，`font-mono text-sm`。
- **變更前 / 變更後圖表**：核心重點。兩欄並排。請參考下方的模式。
- **問題**：一句話。指出痛點。
- **解決方案**：一句話。說明變更內容。
- **優勢**：專案符號，每項不超過 6 個字。例如「測試僅針對一個介面」、「定價邏輯不再洩漏」、「刪除 4 個淺層包裝器」。
- **ADR 提示**（如適用）：在琥珀色調的方框中寫一行。

不要寫解釋性的段落。如果圖表需要一段文字才能被理解，請重新繪製該圖表。

<a id="diagram-patterns"></a>

## 圖表模式

選擇符合候選專案的模式。混合使用。不要讓所有圖表看起來一樣。多樣性是重點之一。

<a id="mermaid-graph-the-workhorse-for-dependencies--call-flow"></a>

### Mermaid 圖表（依賴關係 / 呼叫流程的主力工具）

當重點是「X 呼叫 Y，Y 呼叫 Z，看看這多混亂」時，使用 Mermaid `flowchart` 或 `graph`。將其包裹在 Tailwind 樣式的卡片中，以免顯得突兀。使用 classDef 將洩漏邊緣設為紅色，深層模組設為深色。時序圖很適合用於「變更前：6 次往返；變更後：1 次」。

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.leak.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

<a id="hand-built-boxes-and-arrows-when-mermaids-layout-fights-you"></a>

### 手動建立的方框與箭頭（當 Mermaid 的佈局與你作對時）

模組為帶有邊框和標籤的 `<div>`。箭頭為絕對定位在相對容器上方的內嵌 SVG `<line>` 或 `<path>` 元素。當你希望「變更後」的圖表看起來像一個粗邊框的深層模組，內部細節呈灰色淡化時，請使用此方法，因為 Mermaid 無法以正確的權重渲染此效果。

<a id="cross-section-good-for-layered-shallowness"></a>

### 橫截面（適合展示層級淺薄）

堆疊水平色帶（`h-12 border-l-4`）以顯示呼叫穿過的層級。變更前：6 個薄層，每層都不做什麼。變更後：1 個厚色帶，標註整合後的責任。

<a id="mass-diagram-good-for-interface-as-wide-as-implementation"></a>

### 質量圖（適合展示「介面與實作一樣寬」）

每個模組有兩個矩形：一個代表介面表面積，一個代表實作。變更前：介面矩形幾乎與實作矩形一樣高（淺層）。變更後：介面矩形較短，實作矩形較高（深層）。

<a id="call-graph-collapse"></a>

### 呼叫圖收斂

變更前：以巢狀方框呈現的函式呼叫樹。變更後：同一棵樹收斂為一個方框，現在屬於內部的呼叫在方框內以淡化方式顯示。

<a id="style-guidance"></a>

## 樣式指引

- 偏向編輯風格，而非企業儀錶板。留白要充足。標題可選用襯線字型（`font-serif` 搭配 stone/slate 效果很好）。
- 顏色使用要節制：一個強調色（翡翠綠或靛藍），加上紅色代表洩漏，琥珀色代表警告。
- 圖表高度約 320px，以便變更前/變更後能舒適地並排顯示，無需捲動。
- 在圖表內的模組標籤使用 `text-xs uppercase tracking-wider`，使其看起來像原理圖，而非 UI。
- 唯一的指令碼是 Tailwind CDN 和 Mermaid ESM 匯入。報告其餘部分為靜態：沒有應用程式程式碼，除了 Mermaid 自身的渲染外，沒有其他互動性。

<a id="top-recommendation-section"></a>

## 頂部建議區段

一張較大的卡片。候選專案名稱，一句話說明原因，以及指向其卡片的錨點連結。就這樣。

<a id="tone"></a>

## 語氣

平實簡潔的英文，但架構名詞和動詞直接取自 `/codebase-design` skill。簡潔不是偏離術語的藉口。

**精確使用：** module, interface, implementation, depth, deep, shallow, seam, adapter, leverage, locality。

**絕不替換：** component, service, unit（用於 module）· API, signature（用於 interface）· boundary（用於 seam）· layer, wrapper（用於 module，當意指 module 時）。

**符合風格的措辭：**

- 「Order 接收模組是淺層的：介面幾乎與實作相符。」
- 「定價邏輯跨越接縫洩漏。」
- 「深化：一個介面，一個測試地點。」
- 「兩個介面卡證明瞭接縫的必要性：生產環境用 HTTP，測試環境用記憶體。」

**優勢專案符號** 以術語表中的詞彙命名收益：*「locality：錯誤集中於一個模組」*、*「leverage：一個介面，N 個呼叫點」*、*「介面縮小；實作吸收包裝器」*。不要寫 *「更容易維護」* 或 *「更乾淨的程式碼」*，因為這些詞彙不在術語表中，也不配佔據位置。

不要含糊其辭，不要廢話連篇，不要說「值得注意的是……」。如果一句話可以變成專案符號，就把它變成專案符號。如果一個專案符號可以刪減，就刪掉它。如果一個詞彙不在 `/codebase-design` 術語表中，請先尋找術語表中有的詞彙，再考慮發明新詞。
