---
name: wizard
description: 產生互動式 bash wizard，引導 human 走過手動流程——第三方設定、一次性 migration、A→B 狀態轉換——開啟 URLs、捕捉 values、確認每一步，並寫入 .env files 和 GitHub Actions secrets。
disable-model-invocation: true
---

# Wizard

**Wizard** 是一個 bash script，逐步引導 human 完成手動流程：這種流程手做很煩，每次重新向 AI 解釋也很煩。它會開啟每個 URL、精確說明要點什麼和複製什麼、捕捉 values、寫到正確位置（`.env`、GitHub secrets）、每階段確認，並顯示剩下多少。它可能設定 third-party services、執行一次性 migration，或把專案從一個狀態移到另一個狀態。

愉快的 UX 已由 [template.sh](template.sh) 解決：進度與剩餘時間、confirmation gates、跨平台 URL opening（含 WSL）、隱藏 secret 輸入、idempotent `.env` upserts、`gh secret`/`gh variable` writes，以及 closing summary。**你的工作只是界定流程範圍並撰寫 stages。** `STAGES` marker 以上的 library 在每個 wizard 都相同；一致性就是重點，永遠不要手動編輯它。

Wizard 預設是 ephemeral：為一次執行而建，存到 scratch 或 `scripts/` path，工作完成後刪除。只有當使用者想要一條 repeatable setup path 且它應該留在 repo 時，才 commit 它。

## Process

### 1. Scope the procedure

弄清楚 human 必須採取的每個手動步驟，以及沿途捕捉的每個 value。先讀 repo，不要冷問：

- 設定流程：`.env`、`.env.example`、`.env.*`、`README`、`docker-compose*`、framework config、`.github/workflows/*`（每個 `secrets.*` / `vars.*` reference 都是 wizard 必須產出的 value）。
- Migration 或 transition：目前狀態、目標狀態，以及中間不可逆的 actions。

然後向使用者展示 ordered stages 清單和每個 stage 產生的 values，並確認；他們可能新增、刪除或重排。

**Done when:** 每個 stage 都已依序命名，而且對每個 captured value，你知道：(a) human 從哪裡取得，(b) 寫到哪裡（`.env`、GitHub secret、兩者，或不寫入；有些 stages 是 pure actions），(c) 它是 secret（hidden entry）還是 public。

### 2. Map each stage's journey

為每個 stage 寫出 human 會走的精確路徑：要開哪個 URL、在那裡做什麼、value 顯示在哪裡、填入哪個 variable。例如 "Dashboard → Developers → API keys → Reveal test key → copy"。如果你其實不知道目前 UI 或精確 command，就明說並問使用者或查 docs；永遠不要捏造可能不存在的步驟。

**Done when:** 每個 stage 都能追到陌生人也可照做的具體 instructions。

### 3. Author the wizard

將 `template.sh` 複製到 target path。把 example stage 替換成每個 step 一個 `stage`，依 dependency order 排列。使用 library helpers：`stage`、`say`/`step`、`open_url`、`ask`/`ask_secret`、`write_env`、`set_secret`/`set_var`、`pause`/`confirm`，並把 `TOTAL_STAGES` 和 `TOTAL_MINUTES` 設成誠實估計（這會驅動 remaining-time display）。

維持 template 設下的標準：先開 URL 再要求 value；任何 secret 都用 `ask_secret`；每個 persisted value 都 `write_env`；只有 CI 真正需要的 values 才 `set_secret`；任何 irreversible action 前都 `confirm`。每個 `stage` 會清空畫面，所以只有目前 step 可見；讓每個 stage 聚焦在一件事，避免 human 需要的東西被捲走。不要碰 marker 以上的 library。

### 4. Verify and hand off

- `bash -n <script>`；如果有 `shellcheck`，也執行。
- `chmod +x <script>`。
- 不要自己 end-to-end 執行；它會開瀏覽器並卡在人類輸入。改用靜態 trace：步驟 1 的每個 value 都被捕捉，並落到步驟 1 說的位置；每個 `set_secret` name 都精確符合 CI 中的 `secrets.*` reference。
- 告訴使用者如何執行它。如果它是 repeatable setup path，commit 它並從 README link，讓下一個人執行 script，而不是問 AI。
