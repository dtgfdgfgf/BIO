# 迭代紀律 / Iteration discipline

> **Summary (EN)**: Ten rules ("rule zero plus nine") that I agreed with the LLM coding agents for iterating on a vision inspection algorithm where ground truth is fuzzy and humans do the review. They grew from six to ten in two weeks (2026-09-21 to 10-03), and every version except one was triggered by a concrete failure. The agent drafted the wording and I signed off each version; two of the rules are ones I wrote myself. Every rule is tied to an enforcement method, either a script check or a human sign-off, because rules that rely on the agent's self-discipline had already been shown not to work. [rules.md](rules.md) has the full text; this page is the index.

這是我在專案裡跟 LLM coding agent 約定的「紀律零＋九條」，去掉產品細節後的通用版。適用於任何「真值模糊、要靠人覆核」的視覺檢測迭代：凡是改演算法、門檻、判定條件、ROI、特徵表示法、配方預設值，都算。

全文在 [rules.md](rules.md)。這一頁是索引。

## 三條總原則

1. **完成由檔案裁定，不由 LLM 自述。** diff、分佈、封存集數字、腳本檢查結果才算數。
2. **沒有執行方式的規則，不算規則。** 每條都要對應「腳本檢查」或「人簽核」。
3. **「不改」是合法結論。**「本輪不改」「表示法不足」「此題不能由數據回答」都可以是一輪的產出。

## 十條一覽

| 條 | 一句話 | 執行方式 |
|---|---|---|
| [零](rules.md#紀律零開工前四問階段閘門) | 開工前先答四問（加第五問）；上游沒收斂，不准碰門檻 | 人簽核 |
| [一](rules.md#紀律一先診斷不改碼) | 讀完覆核結果先寫診斷表、先看圖，不改碼；單例不提案；影響範圍不得大於受益範圍 | 機械＋人簽核 |
| [二](rules.md#紀律二每個改動先貼類型標籤) | 每個改動貼類型：bug／定義變更／門檻重校／新增條件／換方法路線，各有前置條件 | 人簽核＋機械 |
| [三](rules.md#紀律三改之前先預測改之後對-diff) | 改前預寫翻轉清單與保護反例，改後對凍結基準 diff，固定輸出四格 | 機械＋人簽核 |
| [四](rules.md#紀律四門檻附分佈不附不算) | 門檻要附全體分佈、±20％ 靈敏度、不同日期取像的重拍結果 | 機械 |
| [五](rules.md#紀律五封存集只回總數) | 封存集事前鎖住、只回總數；改法期間真值與分母不變 | 機械 |
| [六](rules.md#紀律六複雜度記帳並接受不改) | 常數與分支數記帳；每步標計算形狀；每輪最多兩個假說、提案五要素 | 人簽核 |
| [七](rules.md#紀律七效能預算) | 效能用 CPU 核心秒計帳，不看 wall time；定版前必須在預算內 | 機械 |
| [八](rules.md#紀律八方法路線) | 第一個原型前寫方法選擇書、最簡基準實跑，由人在失敗圖上簽「修原路線／換路線」 | 人簽核＋機械 |
| [九](rules.md#紀律九產包前看圖初判) | 產覆核包前，LLM 先看過每一條候選並寫初判，判完再跟人對答案 | 機械＋人簽核 |

## 每一版從哪次失敗長出來

| 版本 | 日期 | 增訂內容 | 觸發案例 |
|---|---|---|---|
| v1 | 09-21 | 六條紀律＋每輪檢核區塊 | 三個比例門檻在 5 張開發圖上調到 51／51 全排除，20 張封存集一跑就破功 |
| v2 | 09-22 | 紀律七：效能預算 | 效能重構讓單張 wall time 快了 4.7 倍，就被當成解決了；實測 CPU 核心秒零變化 |
| v3 | 09-22 | 紀律六加「計算形狀」與「改前預測成本方向」；紀律七加步驟剖析的觸發條件 | 預防性增訂，沒有對應的失敗案例 |
| v4 | 09-23 | 紀律零：開工前四問；紀律一加「先看圖再填表」；每條規則標明執行方式 | 224 萬 token 的多代理分析整輪沒開一張圖，被兩張圖推翻（[growth/04](../growth/04_two_images_vs_2m_tokens.md)） |
| v5 | 09-29 | 紀律八：方法路線；紀律零加第五問；保護反例、預期中間結果、diff 四格、重拍擾動、真值與分母不變、提案五要素 | LLM 一直換路線：自選 6 次方法，0 次留下（[growth/03](../growth/03_method_family_drift.md)）；我寫了[收斂協作規範](../spec/AOI_LLM_convergence_spec.md) |
| v6 | 09-30 | 紀律一加「改動的影響範圍不得大於受益範圍」 | 為了接回一張圖上的斷絲，LLM 想動全圖的二值化 |
| v7 | 10-03 | 紀律九：產包前看圖初判 | 253 條候選只讀數值就產包（[growth/05](../growth/05_human_in_the_loop.md)） |

v7 之後一直到交機，紀律都沒有再增加。

## 誰寫的

- 條文由 LLM 起草，我逐版拍板。
- v6 那一條和紀律九是我直接立的；v5 有一半來自我寫的收斂協作規範。
- 每一版的觸發案例都是真實事件，原專案的修訂紀錄寫明了是哪一輪、哪張圖、哪條紀律沒擋住什麼。

## 怎麼套用到自己的專案

1. 把 [rules.md](rules.md) 放進專案當正本。專案指令檔只放一行指標，各個 skill 只連過去，不複製條文。做法見 [templates/project_instructions.md](../templates/project_instructions.md)。
2. 每次改演算法，回覆都附「紀律檢核」區塊，缺任何一列就不准進覆核包或定版。格式見 [templates/discipline_checklist.md](../templates/discipline_checklist.md)。
3. 能寫成腳本的檢查就寫成腳本。規格列在同一份檔案的 §7。
4. 修訂規則：
   - 任何修改都要附觸發案例。
   - LLM 發現紀律有漏洞或執行成本不合理，先在回覆裡提「修訂提案」，不自行改檔。
   - 版本號跟著檢核區塊走；舊輪次的檢核區塊不改寫。

## 限制

- 大部分紀律是在同一條檢測線、大約兩週內長出來的。
- 很多規則靠「人簽核」執行，最後一道防線還是我有沒有認真看。
- 原專案的檢查腳本綁內部資料格式，沒有放出來；這裡只列規格。
- 這是單一專案、一個人加兩個代理的經驗，不是對 LLM 開發的普遍結論。
