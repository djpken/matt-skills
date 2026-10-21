<a id="skill-mechanics"></a>

# Skill 機制

針對 skill 的特定分支 [`writing-for-agents`](SKILL.md): 當檔案是 skill 時有何變化（frontmatter、呼叫選擇，以及 router skills）。關於撰寫的其他所有內容，請參閱 `SKILL.md` 中的通用參考。

<a id="invocation"></a>

## 呼叫

兩種選擇，取捨兩種負載：

- **model-invoked** skill 保留 `description`，因此 agent 可以自主觸發它，其他 skill 也能夠存取它。您仍然可以輸入其名稱：model-invocation 永遠_包含_使用者存取；描述只會增加 agent 的發現能力，絕不會移除人類的存取許可權。描述是 skill 的頂層上下文指標，強制在所有時間保持載入：以永久上下文負載換取可發現性。如果 model-invoked skill 的內容全是參考資料，它也可以作為共享參考的存放處：另一個 skill 可以呼叫它，因此多個 skill 所需的參考資料可以集中在一個地方。機制：省略 `disable-model-invocation`，並撰寫面向 model 的描述，其中包含觸發分支（`SKILL.md` 中的指標撰寫規則完全適用）。
- **user-invoked** skill 會將描述從 agent 的存取範圍中移除：只有輸入其名稱的人類才能呼叫它，其他 skill 無法呼叫。零上下文負載，但會消耗認知負載：您就是必須記住其存在的索引。機制：設定 `disable-model-invocation: true`；`description` 變為面向人類：一行摘要，移除觸發清單。

僅當 agent 必須自行存取該 skill，或另一個 skill 必須存取它時，才選擇 model-invocation。如果它永遠只由手動觸發，請將其設為 user-invoked，以節省上下文負載。

兩個 user-invoked skills 都需要的共享參考資料無法存放在任何一個之中：由於沒有描述，兩者都無法觸發對方。將其移至 skill 系統之外的普通檔案：任何 skill 都可以指向的外部參考。

<a id="splitting-by-invocation"></a>

## 依呼叫方式拆分

拆分的呼叫切分（序列切分位於 `SKILL.md`）：當您擁有一個獨特的開頭詞，應該單獨觸發該 skill（一個您在提示詞中實際使用的觸發詞），或另一個 skill 必須存取它時，請拆分出一個 model-invoked skill。您必須為新的永遠載入的描述支付上下文負載，因此這種獨立存取必須值得。

## Router skills

當 user-invoked skills 多到超出您的記憶範圍時，這種累積的認知負載可以透過 **router skill** 來緩解：一個 user-invoked skill，列出其他 skill 的名稱以及何時應使用每個 skill，使人類只需記住一個 skill 而非多個。它只能提供提示，永遠無法觸發它們：user-invoked skills 沒有描述，因此除了人類之外，沒有人能存取它們。
