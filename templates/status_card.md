# 檢測線現況表骨架

> **Summary (EN)**: One status card per inspection line. It is the place for volatile status, so the project instruction file never has to hold it. A line counts as frozen only when its status card and the entry route are updated together. Sections are ordered so that a new session reads the machine status and the settled physical facts first.

現況表是「會變的東西」唯一的家。專案指令檔只放一行指向它；**定版＝同步更新該線現況表＋入口路由，否則不算定版。**

```markdown
# <檢測線名稱>：現況表

> STATUS：<研究中／已定版／已接入／已部署>（最後更新 YYYY-MM-DD，<哪一輪或哪個 commit>）
> 新 session 先讀「機台狀態」和「已確立的物理事實」，再讀下方進度。

## 機台狀態
| 項目 | 狀態 |
|---|---|
| 程式掛入主流程（接入） | 是／否，commit <短編號> |
| 版控裡的配方 | 有／無此區段，`enable` 預設 <true／false> |
| 現場在跑（部署） | 是／否，哪台、哪個程式版本 |
| 現場配方和版控配方是否一致 | 一致／不一致：<差在哪> |

「接入」和「部署」分開寫，不要混成一句「已上線」。

## 已確立的物理事實（不隨演算法改版失效）
- <事實>（拍板或量測日期、出處）

## 定義（已拍板）
- ✅ 已拍板（YYYY-MM-DD）：<結論>（原問題出處）
- 只有備註、沒有選選項的，不算拍板。

## 常數與取像條件
| 常數 | 值 | 取像條件 | 出處 |
|---|---|---|---|
取像條件改變時，整張表標「作廢」並另開新表；舊表不改寫。

## 方法路線
### 方法選擇書
（範本：templates/method_selection.md）
### 方法層記分板（只增不改）
（範本：templates/scoreboards.md）

## LLM 看圖記分板（只增不改）
（範本：templates/scoreboards.md）

## 開放問題
| 問題 | 提出輪次 | 掛輪數 | 狀態 |
|---|---|---|---|
同一題掛滿 2 輪沒答＝本線停止出新包，改出問題澄清包。

## 效能
| 日期 | 版本 | 單張 CPU 核心秒 | 預算 | 倍數 |
|---|---|---|---|---|

## 已知限制
- <margin 最小的規則、失效方向（過殺或漏檢）、開發集內數字的免責聲明>

## 量產部署前建議
- <…>

## 歷史（只追加，不改寫）
- YYYY-MM-DD <事件>，詳見 <文件路徑>
```
