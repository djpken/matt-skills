<a id="verifycheck-mode-for-setup-matt-pocock-skills"></a>

# `setup-matt-pocock-skills` 的驗證/檢查模式

此專案不會為 `setup-matt-pocock-skills` 新增專用的驗證/檢查模式（或獨立的驗證 skill）。

<a id="why-this-is-out-of-scope"></a>

## 為何此功能不在範圍內

若新增第二個 skill（或 `--verify` 旗標）來檢查 `docs/agents/*.md` 產出物是否仍符合 seed-template schema，將重複現有設定 skill 在對話中已處理的工作。

預期的工作流程為：**執行 `/setup-matt-pocock-skills` 並指示其驗證目前的設定。** 由於該 skill 由提示詞驅動，維護者可將其範圍限定為驗證流程（「不要重寫任何內容，只需將現有檔案與目前的 seed templates 進行比對並報告差異」），無需建立獨立的程式碼路徑。新增旗標或同級 skill 會將一個已可透過自然語言入口表達的功能拆散，增加介面複雜度。

將設定管理集中於單一 skill，也可避免當 seed templates 演進時，兩個 skill 之間產生維護成本與不一致。

<a id="prior-requests"></a>

## 先前請求

- #106：功能請求：setup-matt-pocock-skills 的驗證/檢查模式
