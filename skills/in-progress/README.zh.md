<a id="in-progress"></a>

# 進行中

Beta。這些 skill 是刻意公開的：請試用並告訴我哪些地方會出錯。在它們升級到穩定類別之前，它們不會包含在 plugin 和頂層 README 中，也不會有檔案頁面，而且可能隨時變更或消失，恕不另行通知。

plugin 不會提供這些。請直接安裝其中一個：

```bash
npx skills@latest add mattpocock/skills --skill=<name>
```

- **[loop-me](./loop-me/SKILL.md)**：透過多次 session，反覆推敲以產出可執行的 workflow 規格，並使用目前目錄作為有狀態的工作區。由使用者呼叫。
- **[writing-beats](./writing-beats/SKILL.md)**：將文章塑造成一段由 beats 組成的旅程，採用選擇你自己的冒險風格。選擇一個起始 beat，只撰寫該 beat，然後轉移到下一個，直到文章達到自然結尾。
- **[writing-fragments](./writing-fragments/SKILL.md)**：透過提問 session 挖掘你的 fragments（異質的寫作片段），並將它們附加到單一檔案中，作為未來文章的原始素材。
- **[writing-shape](./writing-shape/SKILL.md)**：接收一個包含原始素材的 markdown 檔案，並逐段將其塑造成文章，在每個步驟中辯論格式選擇。
- **[claude-handoff](./claude-handoff/SKILL.md)**：將目前的對話移交給一個新的背景 agent，該 agent 會立即接手工作，並透過 `claude --bg` 注入交接摘要。由使用者呼叫。
- **[setup-ts-deep-modules](./setup-ts-deep-modules/SKILL.md)**：將 dependency-cruiser 整合到 TypeScript 儲存庫中，使每個 package 都成為 deep module：實作隱藏在子資料夾中，僅能透過其入口點檔案存取，測試也透過這些檔案來執行。由使用者呼叫。
