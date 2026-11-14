# 示意圖 / Diagrams

> **Summary (EN)**: Hand-drawn Mermaid diagrams only; no customer images or machine screenshots. They cover the convergence order and stage gate, who does what in one iteration round, the development stream versus the sealed holdout stream, the human review data flow, the knowledge architecture shared by two agents, the handoff sequence between agents, and the timeline of how the discipline grew out of failures. GitHub renders them directly.

只放自己畫的示意圖，全部用 Mermaid，GitHub 會直接渲染。不放任何客戶影像或機台截圖。

## 目錄

1. [收斂順序與階段閘門](#1-收斂順序與階段閘門)
2. [一輪迭代：誰負責哪一步](#2-一輪迭代誰負責哪一步)
3. [開發流與稽核流](#3-開發流與稽核流)
4. [覆核資料流](#4-覆核資料流)
5. [知識架構：一個正本，兩個代理](#5-知識架構一個正本兩個代理)
6. [雙代理交接](#6-雙代理交接)
7. [紀律從失敗長出來](#7-紀律從失敗長出來)

---

## 1. 收斂順序與階段閘門

上游還沒收斂時調下游參數，做的功是負的。每輪開工前先過閘門，沒通過只能做上游工作。詳見 [紀律零](../discipline/rules.md#紀律零開工前四問階段閘門)。

```mermaid
flowchart LR
  D["定義<br/>這算不算瑕疵"] --> I["成像<br/>穩定看得到嗎"] --> S["結構建模<br/>每一層都建了嗎"] --> J["判別<br/>候選怎麼分"] --> B["邊界與門檻<br/>線畫在哪"]
```

```mermaid
flowchart TD
  Q["開工前四問＋第五問<br/>LLM 填答案並附證據檔"] --> G{"我簽核：<br/>全部是？"}
  G -->|否| U["只做那一題的上游工作<br/>定義包／結構包／方法包／問題澄清"]
  U --> Q
  G -->|是| H["才可以碰判別與門檻<br/>才可以啟動重型分析"]
```

## 2. 一輪迭代：誰負責哪一步

藍色是 LLM 做的，綠色是腳本裁定的，橘色是我。完成與否由綠色那幾格裁定，不由 LLM 自述。詳見 [growth/02](../growth/02_from_writing_code_to_converging.md)。

```mermaid
flowchart TD
  A["開工前五問：LLM 填、我簽"]:::human
  B["先看圖，寫診斷表<br/>不改碼"]:::llm
  C["提案：最多兩個假說<br/>附保護反例、受益與影響張數"]:::llm
  D["我核准方向"]:::human
  E["預寫翻轉清單"]:::llm
  F["改動"]:::llm
  G["對凍結基準 diff<br/>四格：修好／殘留／新增過殺／新增漏檢"]:::script
  X["退回，先解釋"]:::script
  H["封存集只回總數"]:::script
  I["逐條看圖，寫初判"]:::llm
  J["初判檢查 PASS"]:::script
  K["我逐瑕疵覆核"]:::human
  L["初判 vs 我，計入記分板"]:::script
  A --> B --> C --> D --> E --> F --> G
  G -->|非預期翻轉或保護反例翻轉| X
  X --> C
  G -->|符合預寫| H --> I --> J --> K --> L
  L --> A
  classDef llm fill:#dbeafe,stroke:#1d4ed8,color:#0b1f4d
  classDef script fill:#dcfce7,stroke:#15803d,color:#052e16
  classDef human fill:#ffedd5,stroke:#c2410c,color:#431407
```

## 3. 開發流與稽核流

封存集事前用固定種子抽樣鎖住，改法期間 LLM 不准看逐張結果；改動凍結後才跑，只回總數。詳見 [紀律五](../discipline/rules.md#紀律五封存集只回總數)。

```mermaid
flowchart LR
  N["新影像"] --> S{"事前決定<br/>進哪一條流"}
  S -->|開發流| Dev["可以看逐張細節<br/>可以反覆迭代"]
  S -->|稽核流| Hold["封存集<br/>固定種子抽樣、鎖住"]
  Dev --> Fz["改動凍結"]
  Fz --> Run["跑封存集"]
  Hold --> Run
  Run --> Tot["只回總數<br/>翻轉幾張、候選數怎麼變"]
  Hold -. 一旦看過逐張細節 .-> Down["降級成開發集<br/>下一版重抽"]
```

## 4. 覆核資料流

LLM 的初判和我的判定各自落成檔案，判完才對答案。初判不進覆核介面，避免錨定我的判斷。詳見 [growth/05](../growth/05_human_in_the_loop.md)、[review-pack 範本](../templates/skills/review-pack/SKILL.md)。

```mermaid
flowchart LR
  A["演算法輸出<br/>候選檔"] --> P["LLM 看裁圖<br/>初判檔"]
  A --> UI["覆核介面<br/>看不到初判"]
  UI --> R["我的判定檔<br/>逐條判定、畫漏檢、答題"]
  R --> L["跨輪總帳<br/>矛盾、漏檢、開放問題"]
  R --> X["範例集<br/>＝回歸測試"]
  P --> SB["LLM 看圖記分板"]
  R --> SB
  R --> DX["診斷表<br/>下一輪的起點"]
```

## 5. 知識架構：一個正本，兩個代理

每樣東西只有一個正本。Claude Code 用匯入，Codex 讀正本或腳本產生的鏡射；兩邊不共享聊天。詳見 [growth/07](../growth/07_knowledge_architecture.md)、[專案指令範本](../templates/project_instructions.md)。

```mermaid
flowchart TD
  subgraph SRC["正本（只在這裡改）"]
    AG["AGENTS.md<br/>鐵律＋路由"]
    SK[".claude/skills/<br/>需要時才載入的程序"]
    DOC["docs/<br/>紀律、協定、現況卡"]
  end
  subgraph CC["Claude Code"]
    CM["CLAUDE.md<br/>第一行 @AGENTS.md"]
    MEM["跨 session 記憶<br/>一條一事實"]
  end
  subgraph CX["Codex"]
    CXR["直接讀 AGENTS.md"]
    MIR[".agents/skills/<br/>鏡射，腳本產生"]
  end
  AG -->|匯入| CM
  AG --> CXR
  SK -->|同步腳本| MIR
  SK --> CC
  AG -->|路由| DOC
```

## 6. 雙代理交接

接手者不相信交接卡，拿 Git 和檔案的實況去核對；來源不明又衝突的修改，交給我決定。詳見 [agent-handoff 範本](../templates/skills/agent-handoff/SKILL.md)。

```mermaid
sequenceDiagram
  participant U as 我
  participant A as 代理 A（寫入者）
  participant R as Repo（Git＋檔案）
  participant B as 代理 B（接手者）
  U->>A: 交接給 B
  A->>R: 跑唯讀探測，寫交接卡
  U->>B: 接手
  B->>R: 讀指令、路由、協定、交接卡
  B->>R: 跑唯讀探測
  B->>B: 對卡：HEAD、未提交清單、每個檔案的歸屬
  alt 一致，或差異與本次任務無關
    B->>U: 一句話回報範圍，開始工作
  else 來源不明且與本次任務衝突
    B->>U: 停止寫入，請你決定
  end
```

## 7. 紀律從失敗長出來

兩週內從六條長到十條，除了 v3，每一版都對應一次真實的失敗。詳見 [discipline/](../discipline/)。

```mermaid
timeline
  title 迭代紀律的七個版本
  09-21 : v1 六條紀律 : 開發集 51／51，封存集破功
  09-22 : v2 效能預算 : wall time 快 4.7 倍，核心秒沒變
        : v3 計算形狀 : 預防性增訂
  09-23 : v4 紀律零 開工前四問 : 224 萬 token 被兩張圖推翻
  09-29 : v5 紀律八 方法路線 : LLM 自選 6 次方法，0 次留下
  09-30 : v6 影響範圍不大於受益範圍 : 為一張圖想動全圖二值化
  10-03 : v7 紀律九 產包前看圖初判 : 253 條只讀數值就產包
```

---

## 想用在簡報或貼文

GitHub 上的圖是即時渲染的。要圖檔的話，把 mermaid 程式碼區塊裡的內容貼到任何 Mermaid 編輯器，匯出 PNG 或 SVG 即可。
