# 專案指令骨架：單一正本＋路由

> **Summary (EN)**: A skeleton for the project instruction file that both Claude Code and Codex read. `AGENTS.md` is the single source of truth (Codex reads it natively); `CLAUDE.md` imports it on its first line with `@AGENTS.md` and only adds Claude-specific notes. The file holds hard rules and routes only, never volatile status. It also spells out authorization semantics: which phrases from the human authorize which scope of change. Background in [growth/07](../growth/07_knowledge_architecture.md).

## 設計原則

1. **只放鐵律和路由，不放現況。** 會變的東西（各線進度、目前 commit、驗證資料集）放在定版時一定會更新的現況卡，指令檔只指過去。我踩過的坑：指令檔寫著「驗證主資料集是某某批」，那批早就作廢了，一個多月後才改掉，期間每個 session 一開始都先讀到它。
2. **每樣東西只有一個正本。** Codex 讀 `AGENTS.md`；Claude Code 的 `CLAUDE.md` 第一行用 `@AGENTS.md` 匯入。改規則只改 `AGENTS.md`。
3. **控制大小。** Codex 對專案指令有總量上限（預設 32 KiB，含個人全域那份），所以專案指令檔控制在十 KB 左右，細節一律連到別的文件。
4. **授權語義寫成表。** 哪一句話授權到哪一層，事先講好，LLM 不必每改一個檔案就問「可以嗎」，也不會把反問當成同意。
5. **拍板要寫日期。** 每條鐵律後面標拍板日期，之後才查得到它從哪來、有沒有被推翻。
6. **上層規則要抄進來。** Codex 讀不到 git root 以上的指令檔；放在上層資料夾、對這個專案也有效的紅線，要抄一份進 `AGENTS.md`。Claude 會重複讀到，無害。

## `AGENTS.md` 骨架

```markdown
# AGENTS.md — <專案名>

> 本檔＝專案指令正本：Codex 原生讀取；Claude Code 經 `CLAUDE.md` 第一行 `@AGENTS.md` 匯入。
> 改規則只改這裡，不改 `CLAUDE.md`。
> 入口路由＝`docs/README.md`；各線現況＝`docs/current/` 現況卡。新 session 先走這兩處。

## 最重要的規則
- 這是 <一句話說明專案是什麼、目前在哪個階段>。
- 真相源＝`<原始碼路徑>`。`<原始資料路徑>` 只讀不寫，所有產物寫 `output/`。
- 授權語義（<拍板日期>）：
  - 「你分析正確」「我同意」＝授權把該分析的結論寫進文件（docs／skills／現況卡）；
    不含 `<原始碼路徑>` 與 `<部署目錄>`，那兩處仍要另外取得同意。
  - 「這有問題嗎」「真的嗎」＝要求查證，不是同意。查證不成立就直說說過頭了。
  - 純新增、既有行為零變更的程式＝可直接開工，回報時標明「既有功能未變」。
  - 改既有行為、刪除檔案、改設定格式＝先問。
- 路由：
  - 要動 <子系統> → 先載入 `<map-skill>`
  - 要建置或驗證 → `<build-skill>`
  - 開新檢測線、重校、接入 → `<line-sop-skill>`
  - 出覆核包 → `<review-pack-skill>`
  - 接手另一個代理的工作、交接 → `<handoff-skill>`
- 回覆語言：<語言>。

## 演算法鐵律（使用者拍板）
- 改演算法（程式／門檻／條件／ROI／表示法／配方預設）前必讀 `docs/iteration_discipline.md`；
  每次改動的回覆附「紀律檢核」區塊，缺列不得進覆核包或定版。
- <例：禁止縮圖跑演算法，一律原圖。>
- <例：效能一律以 CPU 核心秒計帳，不看 wall time；預算見紀律七。>
- 常數綁取像條件：換取像條件時，舊常數與舊統計標明作廢，不混用。
- 門檻不盲調；正式判定標準等規格或拍板。

## 建置／驗證
- 依 `<build-skill>`。
- 「接入」（程式掛進去＋版控裡的配方改好）≠「部署」（現場機台真的在跑）；講機台狀態時分開講。
- 有重建執行檔時，回覆結尾列出 exe 的完整路徑，以及它內嵌的程式版本（commit 短編號；未 commit 標「未 commit」）。

## 雙代理協作
- 規則正本＝`docs/agent_collaboration_protocol.md`；接手先跑唯讀探測腳本。
- skills 正本＝`.claude/skills/`；`.agents/skills/` 是鏡射，由同步腳本產生，不手改。
- 同一 working tree 同時只有一個寫入者，由使用者指定；不確定就先問。

## 環境與紅線（自上層指令檔抄錄；Codex 讀不到 git root 以上的檔案）
- 原始資料只讀不寫；客戶交付物不覆蓋，改版另存新檔。
- <作業系統、shell、編碼的地雷>

## 文檔維護
- 定版＝同步更新該線現況卡＋入口路由，否則不算定版。
- 歷史檔不改寫、不搬移、不刪除（工具鏈寫死路徑，舊結論本身也是證據）。
```

## `CLAUDE.md`

```markdown
@AGENTS.md

## Claude Code 專用補充
- 專案規則正本＝上行匯入的 `AGENTS.md`（Codex 讀同一份）；改規則改那裡，本檔只放 Claude Code 才需要的補充。
```

## 入口路由 `docs/README.md` 該有什麼

- **§0 動線**：新 session 依序讀哪些檔。
- **現況區**：每條檢測線一列，指向它的現況卡（範本見 [status_card.md](status_card.md)）。
- **名詞對照**：舊稱和新稱、程式識別字和白話名稱。例如使用者更正過的名詞，新文件用新稱呼，程式識別字和歷史檔照舊。
- **危險品警告**：哪些歷史文件的數字已作廢、為什麼。
- **工具清單**：每支工具一行，寫用途和正本位置。

## 授權語義表（我實際用的版本）

| 我說的話 | 授權範圍 |
|---|---|
| 「你分析正確」「我同意」 | 把這個分析的結論寫進文件；不含機台原始碼和部署目錄 |
| 「這有問題嗎？」「真的嗎？」 | 要求查證，**不是同意** |
| 純新增、既有功能完全不變的程式 | 可以直接開工，回報時標明「既有功能未變」 |
| 改既有行為、刪東西、改配方格式 | 先問 |
| 交機期 | 每輪只提「下一步」，短到一個畫面以內；我說同意才做 |
