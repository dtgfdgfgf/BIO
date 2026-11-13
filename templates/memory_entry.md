# 跨 session 記憶條目格式

> **Summary (EN)**: An LLM agent starts every session with no memory. I kept a cross-session memory where each entry holds exactly one fact, with a "why" and a "how to apply" line, and a one-line index that is loaded at the start of every session. Superseded entries are marked, not deleted, because a wrong turn is also information. Before relying on an entry that names a file or function, the agent checks that it still exists.

這是 Claude Code 的跨 session 記憶。Codex 看不到它，只能讀專案裡的檔案，所以**規則正本仍然要寫在專案文件裡**，記憶只放「怎麼跟這個人、這個專案合作」的事。

## 1. 一條記憶的格式

每條一個檔案，一個事實：

```markdown
---
name: analysis-correct-means-write-authorization
description: 使用者說「你分析正確」＝授權把結論寫進文件，不含原始碼與部署目錄（2026-09-23）
metadata:
  type: feedback
---

使用者 2026-09-23：說「你分析正確」或「我同意」，就是授權把該分析結論寫進文件（docs／skills／現況卡），不必另外再問。

**Why:** 每寫一份文件都問「可以嗎」，拖慢節奏，而且讓「同意」變成名義上的。

**How to apply:** 收到這兩句話，直接把結論寫進對應文件，回報寫了哪些檔。原始碼和部署目錄仍要另外取得同意。使用者反問「這有問題嗎」＝要求查證，不是同意。相關：[[authorization-semantics]]。
```

### 四種類型

| type | 放什麼 | 例 |
|---|---|---|
| user | 使用者是誰、背景、偏好 | 「不寫演算法，但能在失敗圖上判斷該修還是該換」 |
| feedback | 使用者給的工作方式指示，包括糾正和確認過的做法 | 「解釋要用圖不要用講的」 |
| project | 進行中的工作、目標、限制，而且從程式和 git 看不出來的 | 「交機期只做離線可驗的工作」 |
| reference | 外部資源的指標 | 「客戶規格書在某雲端資料夾」 |

## 2. 索引檔

每個 session 開始時自動載入。一條一行，只放標題和一句話：

```markdown
# MEMORY

- [「分析正確」＝寫入授權 2026-09-23](analysis-correct-means-write-authorization.md) — 可寫文件，不含原始碼與部署目錄
- [先看圖再看數 2026-09-22](look-at-images-before-numbers.md) — 紀律零四問；數據答不了的交還使用者
- [取像域改以實拍為準](imaging-domain-switch.md) — 【已被取代】見 image-domain-light-change
```

## 3. 規則

1. **一條一個事實。** 附「為什麼」和「怎麼用」。少了「為什麼」，下次遇到邊界情況就無法判斷。
2. **日期寫絕對日期。**「昨天」「上週」過了就沒意義。
3. **不存 repo 已經記錄的東西。** 程式結構、git 歷史、文件裡已經有的，不要再存一份；存了就是兩個正本。
4. **被取代的不刪，標【已被取代】並指向新的那條。** 在已作廢的取像條件下得到的結論，標【作廢域】。錯過的路也是資訊。
5. **使用前先驗證。** 記憶裡寫到某個檔案、函式、設定，引用之前先確認它現在還存在。記憶反映的是寫下那一刻的事實。
6. **先找再寫。** 新增之前先查有沒有一條已經在講這件事，有就更新那條。
7. **索引太長就壓縮。** 索引每條只留一句；細節移回各條本文。

## 4. 記憶 vs 專案文件

| 放記憶 | 放專案文件 |
|---|---|
| 使用者的溝通偏好、授權語義的由來 | 授權語義表本身（兩個代理都要讀到） |
| 某次踩坑的教訓與觸發情境 | 由教訓長出來的規則（紀律、skill） |
| 環境地雷（某台電腦缺什麼） | 建置與驗證程序 |
| 跨專案的個人目標 | 各線現況、拍板紀錄 |

判斷方法：另一個代理也需要知道的，就寫進專案文件。
