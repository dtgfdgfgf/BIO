# 範本 / Templates

> **Summary (EN)**: Copy-and-adapt templates distilled from the project: the project instruction skeleton (one source of truth plus routing, with authorization semantics), three generalized skills plus a skill skeleton, the cross-session memory entry format, the status card for each inspection line, the method selection sheet and family-switch declaration, the two scoreboards, and the per-round discipline checklist with its supporting forms. Everything has been rewritten for general use; nothing here is the original project's file. Content is in Chinese.

從專案裡實際在用的檔案改寫成通用版。產品名稱、路徑、門檻數值都拿掉了，換成 `<尖括號>` 佔位。

## 清單

| 範本 | 用途 | 相關 |
|---|---|---|
| [project_instructions.md](project_instructions.md) | 專案指令骨架：`AGENTS.md` 正本＋`CLAUDE.md` 匯入、授權語義表、入口路由 | [growth/07](../growth/07_knowledge_architecture.md) |
| [skills/_skeleton/SKILL.md](skills/_skeleton/SKILL.md) | Skill 骨架與寫法原則 | [growth/07](../growth/07_knowledge_architecture.md) |
| [skills/review-pack/SKILL.md](skills/review-pack/SKILL.md) | 覆核包的四種型別、組成、資料契約、回讀規則 | [growth/05](../growth/05_human_in_the_loop.md)、紀律零／一／九 |
| [skills/line-sop/SKILL.md](skills/line-sop/SKILL.md) | 檢測線「研究 → 定版 → 接入」三階段 SOP | 紀律四／七／八 |
| [skills/agent-handoff/SKILL.md](skills/agent-handoff/SKILL.md) | 雙代理接手、交接卡、寫入權、skills 鏡射 | [growth/07](../growth/07_knowledge_architecture.md) |
| [memory_entry.md](memory_entry.md) | 跨 session 記憶的條目格式、索引與規則 | [growth/07](../growth/07_knowledge_architecture.md) |
| [status_card.md](status_card.md) | 每條檢測線的現況卡 | [growth/07](../growth/07_knowledge_architecture.md) |
| [method_selection.md](method_selection.md) | 方法選擇書、方法包、換族聲明 | 紀律八、[growth/03](../growth/03_method_family_drift.md) |
| [scoreboards.md](scoreboards.md) | 方法層記分板、LLM 看圖記分板 | 紀律八、九 |
| [discipline_checklist.md](discipline_checklist.md) | 每輪紀律檢核區塊、開工前五問、診斷表、提案、翻轉清單、初判檔、機械檢查規格 | [discipline/](../discipline/) |

## 怎麼用

- **skills**：把 `skills/<名稱>/` 整個資料夾複製到你專案的 `.claude/skills/`，把 `<尖括號>` 換成你的路徑和名稱。Codex 用的話放 `.agents/skills/`，或照 [agent-handoff](skills/agent-handoff/SKILL.md) 的做法用腳本鏡射。
- **專案指令**：從 [project_instructions.md](project_instructions.md) 的骨架開始，只放鐵律和路由；現況寫進 [status_card.md](status_card.md)。
- **紀律相關的表格**：先讀 [discipline/rules.md](../discipline/rules.md)，再挑你需要的表格。不必十條一次全上；我自己也是兩週內一條一條長出來的。

## 注意

- 這些是改寫過的通用版，不是原專案的檔案。原專案的檢查腳本和覆核介面綁內部資料格式，沒有放出來；範本裡列的是規格和最小欄位。
- 範本反映的是「一個人加兩個代理」的規模，沒有在團隊裡驗證過。
