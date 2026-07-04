---
name: resolving-merge-conflicts
description: 用於需要解決正在進行中的 git merge/rebase 衝突時。
---

1. **查看目前狀態**：查看 merge/rebase 的當前狀態。檢查 git 歷史記錄和衝突的檔案。

2. **找到每個衝突的主要來源**。深入理解每個更改的原因以及原始意圖是什麼。閱讀 commit 訊息、查看 PR、查看原始 issue/ticket。

3. **解決每個 hunk。** 盡可能保留兩方的意圖。如果不相容，選擇符合 merge 聲明目標的那個，並記錄取捨。**不要**發明新的行為。務必解決；絕不 `--abort`。

4. 找出專案的**自動化檢查**並執行它們——通常是型別檢查、然後測試、然後格式化。修復 merge 破壞的任何東西。

5. **完成 merge/rebase。** 暫存所有東西並提交。如果是 rebase，繼續 rebase 流程直到所有 commit 都被 rebase。
