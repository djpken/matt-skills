<a id="out-of-scope-knowledge-base"></a>

# 範圍外知識庫

儲存庫中的 `.out-of-scope/` 目錄用於儲存被拒絕的功能請求的持久化記錄。它有兩個用途：

1. **機構記憶**：記錄功能被拒絕的原因，以便在問題關閉時不會遺失相關推理過程
2. **去重**：當新的問題與先前的拒絕案例相符時，skill 可以顯示先前的決定，而不是重新爭論

<a id="directory-structure"></a>

## 目錄結構

```
.out-of-scope/
├── dark-mode.md
├── plugin-system.md
└── graphql-api.md
```

每個**概念**對應一個檔案，而非每個問題對應一個檔案。多個請求相同內容的問題會歸類在同一個檔案下。

<a id="file-format"></a>

## 檔案格式

檔案應以輕鬆、易讀的風格撰寫，更像是一份簡短的設計檔案，而非資料庫條目。請使用段落、程式碼範例和示例，讓推理過程清晰且對初次接觸者有用。

```markdown
# Dark Mode

This project does not support dark mode or user-facing theming.

## Why this is out of scope

The rendering pipeline assumes a single color palette defined in
`ThemeConfig`. Supporting multiple themes would require:

- A theme context provider wrapping the entire component tree
- Per-component theme-aware style resolution
- A persistence layer for user theme preferences

This is a significant architectural change that doesn't align with the
project's focus on content authoring. Theming is a concern for downstream
consumers who embed or redistribute the output.

```ts
// 目前的 ThemeConfig 介面並非為 runtime 切換而設計：
interface ThemeConfig {
  colors: ColorPalette; // 單一色盤，在 build time 解析
  fonts: FontStack;
}
```

## Prior requests

- #42: "Add dark mode support"
- #87: "Night theme for accessibility"
- #134: "Dark theme option"
```

<a id="naming-the-file"></a>

### 檔案命名

使用簡短且具描述性的 kebab-case 名稱來命名概念：`dark-mode.md`、`plugin-system.md`、`graphql-api.md`。名稱應足夠具辨識度，讓瀏覽目錄的人無需開啟檔案即可理解被拒絕的內容。

<a id="writing-the-reason"></a>

### 撰寫原因

原因應具實質內容：不是「我們不想要這個」，而是為什麼。好的原因會參考：

- 專案範圍或理念（「此專案專注於 X；主題設定是下游的關注點」）
- 技術限制（「支援此功能需要 Y，這與我們的 Z 架構衝突」）
- 策略決定（「我們選擇使用 A 而非 B，因為...」）

原因應具有永續性。避免引用暫時性的情況（「我們現在太忙了」）；這些不是真正的拒絕，而是延期。

<a id="when-to-check-out-of-scope"></a>

## 何時檢查 `.out-of-scope/`

在分診期間（步驟 1：收集背景資訊），請閱讀 `.out-of-scope/` 中的所有檔案。在評估新問題時：

- 檢查請求是否與現有的範圍外概念相符
- 匹配是基於概念相似性，而非關鍵字：「夜間主題」與 `dark-mode.md` 相符
- 如果有匹配，請向維護者顯示：「這與 `.out-of-scope/dark-mode.md` 類似。我們之前拒絕了此功能，因為 [原因]。您現在的看法是否相同？」

維護者可能會：

- **確認**：新問題會被加入現有檔案的「先前請求」清單，然後關閉
- **重新考慮**：範圍外檔案會被刪除或更新，問題將進入正常的分診流程
- **不同意**：問題相關但不同，繼續進行正常的分診

<a id="when-to-write-to-out-of-scope"></a>

## 何時寫入 `.out-of-scope/`

僅當**增強功能**（非 bug）被*拒絕*為 `wontfix` 時。這同樣適用於增強功能的 PR：被拒絕的 PR 會記錄在此處，以便相同的請求不會以新的程式碼形式再次出現。

當某項內容因**已實作**而關閉為 `wontfix` 時，請**不要**在此處寫入。那是已建置的功能，而非被拒絕的功能；記錄它會以虛假拒絕汙染去重檢查。相反地，關閉評論應指向該功能已存在的位置。

流程：

1. 維護者決定功能請求超出範圍
2. 檢查是否已存在匹配的 `.out-of-scope/` 檔案
3. 如果有：將新問題附加到「先前請求」清單
4. 如果沒有：建立新檔案，包含概念名稱、決定、原因和第一個先前請求
5. 在問題上發布評論，解釋決定並提及 `.out-of-scope/` 檔案
6. 使用 `wontfix` 標籤關閉問題

<a id="updating-or-removing-out-of-scope-files"></a>

## 更新或移除範圍外檔案

如果維護者改變了對先前被拒絕概念的立場：

- 刪除 `.out-of-scope/` 檔案
- skill 不需要重新開啟舊問題；它們是歷史記錄
- 觸發重新考慮的新問題將進入正常的分診流程
