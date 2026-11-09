# llm-aoi-convergence

**以 LLM coding agent 接手並改造一套工業 AOI 機台程式：我從中學到的收斂方法。**
**Taking over and rebuilding an industrial AOI machine program with LLM coding agents — what I learned about making them converge.**

> 草稿（2026-11）。內容陸續補上，狀態見下方「導覽」。
> Draft (2026-11). Content is being added; see Contents for status.

---

## 專案概述 / Overview

2026 年 6 月到 11 月，我接手一套既有的工業 AOI（自動光學檢測）機台程式，把它改造成檢測新樣品：電極試片的外觀瑕疵，包括毛屑、貼合偏移、點藥填充不足、溢藥。系統是 C# WinForms＋OpenCV，搭配 PLC 與 6 台工業相機。從接手、研究、接入檢測線到交機，**程式碼全程由 LLM coding agent（Claude Code、Codex）產生**。

From June to November 2026 I took over an existing industrial AOI (automated optical inspection) machine program and rebuilt it to inspect a new product: cosmetic defects on electrode test strips (fibers, misalignment, insufficient reagent fill, reagent overflow). The stack is C# WinForms + OpenCV with a PLC and six industrial cameras. From takeover through research, line integration and handover, **all code was written by LLM coding agents (Claude Code and Codex)**.

## 我的角色 / My role

LLM 寫程式；我負責方向、瑕疵定義、拍板、驗證設計，以及約束 LLM 的規則。這個 repo 整理的就是後者：在真值模糊、要靠人覆核的視覺檢測裡，怎麼讓 LLM 的迭代真正收斂，而不是在開發集上越調越好看。

The agents wrote the code. I owned direction, defect definitions, sign-offs, verification design, and the rules that constrain the agents. This repo is about the latter: how to make LLM iterations actually converge in vision inspection where ground truth is fuzzy and humans do the review, instead of just looking better on the development set.

## 時間軸 / Timeline

| 期間 / Period | 階段 / Phase |
|---|---|
| 2026-06 | 接手：唯讀分析、建立 git 基準線、讓開發機能離線建置與執行 / Takeover: read-only analysis, git baseline, offline build and run |
| 2026-07 | 現代化：升級 .NET Framework 與 OpenCV 封裝庫、相機模擬器走完整生產管線 / Modernization: framework and OpenCV wrapper upgrade, camera simulator through the full production pipeline |
| 2026-07～08 | 第一批檢測線：研究→移植→接入、參數旋鈕化、逐位零迴歸；後因取像條件與範圍變更作廢 / First inspection lines with bit-exact regression; later invalidated by imaging and scope changes |
| 2026-09 | 上機：取像觸發電氣除錯、雙代理協作機制、檢測範圍重設、迭代紀律成形 / On-machine: trigger-line debugging, dual-agent protocol, scope reset, iteration discipline |
| 2026-10 | 新取像條件下三條檢測線接入、現場參數頁、客戶報表 / Three lines integrated under new imaging, on-site parameter page, reports |
| 2026-10～11 | 交機：穩健性、記憶體釋放、版本追溯、交接文件 / Handover: robustness, memory release, build traceability, handover docs |

## 收斂順序 / Convergence order

```mermaid
flowchart LR
  A[定義<br/>Definition] --> B[成像<br/>Imaging] --> C[結構建模<br/>Structure] --> D[判別<br/>Discrimination] --> E[邊界與門檻<br/>Boundary]
```

上游沒收斂時調下游參數，做的功是負的。 / Tuning downstream while upstream is unsettled is negative work.

## 成長重點 / Key takeaways

1. **要改的不是提示詞，是「什麼算完成」。** 完成與否由檔案（diff、分佈、封存集數字）裁定，不由 LLM 自述。
   *Change the definition of done, not the prompt wording. Files decide completion, not the agent's self-report.*
2. **LLM 在基準失敗時會換更強的工具，而不是修失敗原因。** 方法選擇要在第零天由人在失敗圖上簽核。
   *When a baseline fails, an LLM tends to swap in a stronger tool instead of fixing the cause. A human should sign off the method on the failure images, on day zero.*
3. **每輪關掉一個開放問題，而不是改善一個數字。**
   *Each round should close one open question, not improve one number.*
4. **多代理放大的是執行品質，不是方向正確性。**
   *Multi-agent runs amplify execution quality, not directional correctness.*
5. **LLM 的眼睛要用在人前面，並留下可對答案的紀錄。**
   *Let the LLM look before the human does, and keep a record that can be scored against the human.*
6. **LLM 專案要有單一正本、可載入的程序知識、跨 session 記憶與明確的授權語義。**
   *An LLM project needs one source of truth, loadable procedures, cross-session memory and explicit authorization semantics.*

## 導覽 / Contents

| 路徑 / Path | 內容 / What | 狀態 / Status |
|---|---|---|
| [`spec/`](spec/) | 我寫給 LLM agent 的收斂協作規範 / The convergence spec I wrote for the agents | ✅ |
| [`growth/`](growth/) | 成長整理：每篇「當時怎麼做→出了什麼事→學到什麼→現在怎麼做」 / Growth notes | 3／8 |
| [`discipline/`](discipline/) | 從實際失敗訂出的迭代紀律（通用版） / Iteration discipline derived from real failures | 待寫 / TODO |
| [`templates/`](templates/) | 可直接套用的範本 / Reusable templates | 待寫 / TODO |
| [`diagrams/`](diagrams/) | 自繪示意圖 / Hand-drawn diagrams | 待寫 / TODO |

## 不在這裡的東西 / What is not here

- 原始碼、影像、客戶資料、門檻常數都屬於雇主與客戶，不放在這裡；所有名稱與編號已去識別化。
- 文中數字是開發期的內部覆核結果，不是量產驗收準確率。
- Source code, images, customer data and threshold constants belong to my employer and the customer and are not included. Names and identifiers are anonymized.
- Numbers quoted here come from internal review during development, not from production acceptance testing.
