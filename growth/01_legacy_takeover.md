# 01 接手遺留系統：先唯讀、先建基準線、先確認哪一份才是正本

> **Summary (EN)**: I inherited a C# WinForms AOI machine program of about 50,000 lines with a PLC, six cameras and a marking-pen servo; it would not even build on a development machine. The first commit was a baseline, not a change, and the first week was read-only analysis by the agent: file-by-file dossiers, a risk report, and a list of things to ask the predecessor. Then it turned out we had analyzed an older copy; the formal delivery was a different tree with structural differences. Static conclusions were overturned by actually running the build. Later, renaming one string compiled cleanly but silently disconnected all parameters, and a "bug" that looked obvious was quietly holding up another behavior on the shop floor. I now confirm the source of truth first, verify claims by running things, map hidden bindings before renaming, and never fix legacy code literally without checking the real operating rule.

---

## 背景

2026 年 6 月，我接手一套前人留下的工業 AOI 機台程式：

- C# WinForms，約 5 萬行。
- 一個主視窗類別裡塞滿了長駐執行緒和全域狀態。
- 搭配 PLC、6 台相機和打印筆伺服。
- 開發機上編不起來。

目標是把它改造成檢測新的樣品。我不寫 C#，也不寫 PLC；程式碼全部由 LLM 產生。

## 1. 當時怎麼做

- **第一個 commit 是基準線**：把程式碼現狀原封不動放進 git，再開始做任何事。
- **第一週全程唯讀**：LLM 逐檔深讀，寫了 10 份卷宗，再整理出一份風險報告、一份「請前人提供」清單，以及一份新舊樣品規格對照的空白模板。
- **分析的對象**：當時工作區裡的那一份程式碼。
- **早期的結論**：大多是讀程式碼推論出來的，例如「缺哪些套件所以編不起來」。

## 2. 出了什麼事

### 分析錯了版本

一週後才確認，前人正式交付的那一包（文件、圖片、程式、PLC）才是真正的正本。兩份程式不只是行號不同，行為和結構都不一樣。前面所有分析的行號和部分結論都要重新核對。

### 讀程式碼的推論，被實際執行推翻

- 推論是「缺 NuGet 套件，所以編不起來」。實際跑一次建置，發現套件會自動還原；真正擋住編譯的，是一個不在原始碼裡的自製控制項 DLL。
- 推論是「缺相機的 DLL」。結果它其實就在原始碼樹裡，真正缺的是另一個。

### 改一個名字，參數全部靜默失聯

後來要把一個規格名改成新名字。結果發現這個字串同時是：

- 介面上的選項
- 資料庫的表名
- 四種設定檔的檔名前綴

程式碼裡沒有任何地方集中定義它。只改一處的話，**編譯會過、畫面看起來也正常，但參數和門檻全部讀不到**。這是靠啟動後的截圖，看到一個警告對話框才發現的。

### 看起來像 bug 的，不一定該照字面修

「開始檢測」前有一道急停檢查，讀錯了旗標，等於沒有擋，看起來是明顯的 bug。查證後發現：

- 照字面修好，每次開始檢測都會被擋下，違反現場的操作規則。
- 同一個旗標還「意外地」讓每次復歸都順便把筆軸歸位。直接刪掉旗標，會壞掉另一件事。

### 第一批檢測線全部作廢

7、8 月做的第一批檢測線，後來因為取像條件和檢測範圍都變了，常數全部作廢，詳見 [06](06_imaging_domain_and_measurement_trust.md)。留下來的是工具和方法，不是數字。

## 3. 學到什麼

1. **先確認哪一份才是正本，再開始分析。** 分析錯的版本，做得越細，浪費越多。
2. **第一個 commit 是基準線，不是修改。** 從此任何改動都能比對、都能回復。
3. **讀程式碼得出的結論，要用實際執行驗證。** 建置、啟動、截圖，比推論可靠。
4. **遺留系統最危險的是隱性契約。** 一個字串可能同時綁著好幾個地方。改之前先找出所有綁定處；驗證時要看畫面，不能只看程式有沒有活著。
5. **不照字面修舊程式。** 舊程式的「錯」，可能正撐著現場的某個行為。先查出真正的現場規則，再交給我決定。
6. **開發機要能離線跑完整流程。** 這是之後所有迭代的前提。

## 4. 現在怎麼做

### 接手前三件事

- **建基準線**：第一個 commit 只放現狀。
- **定紅線**：分析和說明可以自由做；**改任何既有原始碼之前，都要先取得我的明確同意**。這條一直維持到交機，中間只放寬過一次：純新增、既有功能完全不變的程式可以直接做。
- **定正本**：寫明哪一份程式、哪一份文件是正本，其他都是歷史參考。舊的分析文件開頭標上「歷史」，但不改寫內容。

### 分層盤點

照這個順序盤點：產品、規格與驗收 → 系統與硬體 → 程式結構 → PLC。

- 文件多的層次，用多代理分頭做逐份摘要，再加一輪對抗式查核，專門找遺漏和互相矛盾的地方。
- PLC 梯形圖由 LLM 逐頁帶我讀，每頁附一段白話版的比喻；再把梯形圖和 C# 程式之間的對應關係，整理成一份參考文件。
- 讀不出答案的，一律列進「待確認」，不用猜的補上。

### 風險報告先寫，修改後做

接手時的三大風險：

- 記憶體沒有釋放
- PLC 沒有看門狗，也不會斷線重連
- 大量空的例外處理，把演算法錯誤偽裝成 NG

這三項後來在交機階段逐一處理，詳見 [08](08_handover_engineering.md)。

### 先讓開發機能離線跑

- 升級 .NET Framework 和 OpenCV 封裝庫。
- 加一個離線組態，跳過 PLC 和打印筆的連線。
- 寫一個相機模擬器，把存好的影像從相機接收端注入，**走完整的生產管線**，而不是另開一條測試捷徑。

### 改名或改鍵之前

- 先搜尋所有綁定處，列出清單，再一次改齊。
- 驗證時要啟動程式截圖，確認沒有任何警告對話框，不能只看程式有沒有在跑。

## 後來

- 接手時寫的卷宗和程式地圖，後來濃縮成一個「機台程式地圖」skill：用方法名當錨點，不用行號，詳見 [07](07_knowledge_architecture.md)。
- 「不照字面修」變成慣例。那道急停檢查，最後是由我決定擋法之後才改的：開始檢測前，必須確認機台已經完成復歸。這項修改已經 commit，還在等上機驗證。

## 限制

- 第一週有一部分分析做在錯的版本上，這是實際付出的代價。
- 這套做法的前提是前人的程式和文件都拿得到；完全沒有交付文件的情況，我沒有經歷過。
