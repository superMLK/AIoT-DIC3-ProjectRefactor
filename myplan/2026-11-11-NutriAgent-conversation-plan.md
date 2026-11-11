# 2026-11-11 NutriAgent 規劃紀錄

> 來源：Proposal 簡報（25 頁）、DESIGN.md v2.0.0，以及 2025–2026 外部研究與新聞。
> 目的：補強研究的 **Interesting／Innovation／Important**，並依簡報 p.2 的六段敘事主線（A→F）分配證據。

---

## 一、專案定位

- **名稱**：NutriAgent
- **中文全名**：結合穿戴式 IoT、臨床營養規則與大型語言模型之對話式外食決策代理人
- **英文全名**：A Grounded Conversational LLM Agent for Real-Time Dietary Decisions Integrating Wearable IoT and Clinical Nutrition Rules
- **核心理念**：規則管安全與數字，LLM 管理解與表達。
- **收尾句**：安全由架構保證，語言交給 LLM，數字只來自資料庫。

---

## 二、A→F 證據分配

### A｜Attention Getter：讓台下覺得「這跟我有關」（p.3）

- 國民營養調查：逾 63% 的 18 歲以上國人鈉攝取量超過建議標準。
- 台灣慢性腎臟病盛行率 11.9%，認知率僅 3.5%；每年約 1.2 萬人新進入透析。
- 2022 年新增透析患者以 40–64 歲最多，呼應情境故事中 45 歲的陳先生。
- OpenAI（2026/1）：每天超過 4,000 萬人向 ChatGPT 詢問健康問題，約 70% 的健康對話發生在診所看診時間以外。

**一句話**：每天中午，你的便當裡有鹽，手機裡有 AI，但兩者之間沒有人把關。

### B｜But：LLM 給的不是安全答案（p.4–6、p.9）

- **溴化鈉中毒病例**（Annals of Internal Medicine: Clinical Cases, 2025/8）：一名 60 歲男性詢問 ChatGPT 食鹽替代品，以溴化鈉替代三個月後中毒住院。醫師未取得原始對話紀錄，引用時寫「病例報告指出」。
- **ECRI 2026**：AI 聊天機器人誤用列為年度頭號醫療科技危害。
- **Nature Medicine RCT（2026/2，N = 1,298）**：受試者使用 GPT-4o、Llama 3、Command R+，找出相關病況低於 34.5%、正確處置低於 44.2%，皆未優於對照組；標準測驗無法預測真人使用時的失敗。
- 保留原簡報的便當鈉低估 [5]、腎病飲食鉀超標 [30]。

**一句話**：LLM 會考醫師執照，不代表它能幫你點午餐。

### C｜Cure：解方方向有權威背書（p.10–15）

- Nature Medicine 作者指出，下一步需要研究如何在高風險情境中設計更可靠、更具確定性的對話式 LLM 工具，直接呼應 Research Gap。
- 溴化鈉案後，專家呼籲整合醫學知識庫、自動風險標記與人機共同把關，分別對應 K2 臨床規則庫、拒絕紀錄 V、Human-in-the-Loop 回饋。

**一句話**：別人在呼籲的，我們把它做成架構。

### D｜Development：架構選擇有產業對照（p.16–21）

- **Google Health Coach**（2026/5，Gemini 驅動）：結合健身、睡眠、環境、營養與病歷，驗證「穿戴＋情境＋營養」是業界方向。
- **ChatGPT Health**（2026/1）：可連結病歷入口與健康 App。
- **FoodGuardBench／FoodGuard-4B**（arXiv 2604.01444, 2026/4）：以模型做食安防護。
- **差異化**：它們以資料整合或另一個模型防護；NutriAgent 以確定性規則防護，可單元測試、可稽核、不會被越獄。避免宣稱對方「沒有安全機制」。

### E｜Experiments：為什麼要做真人實測（p.22）

- 標準醫學測驗與模擬病人互動，都無法預測真人使用時的失敗。
- 以 LLM 模擬受試者時表現優於真人，且與真人結果僅弱相關 → 支持 10–15 人午餐實測。
- 作者建議醫療場域公開部署前先做系統性真人測試 → 支持 B3（Gemini Zero-Shot 不經閘門）作為對照。

### F｜Findings：成果接得上政策（p.23–24）

- 食藥署與國健署推出營養標示「紅黃綠」分級（2026/4）→ NutriAgent 把靜態標示變成點餐當下的個人化決策。
- 國健署 2026「營養及健康飲食促進方案」鼓勵結合數位科技與職場、社區場域，推動行為改變。
- 2026 國家慢性腎臟病防治政策建言書，呼籲將「降低新發透析人數」列為核心 KPI。

**收尾句**：當 AI 已經是 4,000 萬人的健康入口，安全不能交給提示詞，要交給架構。

---

## 三、IEEE 風格摘要

### 摘要

外食已成為日常飲食型態，而逾 63% 之成年國人鈉攝取量超過建議標準，慢性腎臟病盛行率達 11.9%，認知率僅 3.5%。同時，大型語言模型（LLM）已成為民眾取得健康資訊的主要管道之一，然而其飲食建議存在數值不穩定與安全性不足之問題。既有研究指出，LLM 普遍低估餐點鈉含量，且一般民眾使用 LLM 判斷病況之表現並未優於對照組。本文提出 NutriAgent，一個結合穿戴式物聯網、臨床營養規則與 LLM 之接地式對話外食決策代理人。其架構採「規則負責安全與數值，LLM 負責理解與表達」之分工：臨床與過敏原硬約束閘門置於 LLM 之前，生成回覆之數值須與資料庫一致，否則以確定性模板取代，並於 LLM 失效時自動降級為離線引擎。初步功能測試顯示，臨床與過敏原違規率均為 0%，熱量平均絕對誤差為 114.48 kcal。

**關鍵詞**：大型語言模型、對話式代理人、健康感知推薦系統、臨床決策支援、穿戴式物聯網、接地生成

### Abstract

Eating out has become a dominant dietary pattern, yet over 63% of adults in Taiwan exceed recommended sodium intake, and chronic kidney disease affects 11.9% of the population with an awareness rate of only 3.5%. Meanwhile, large language models (LLMs) have emerged as a primary channel for public health information, although their dietary advice suffers from numerical instability and insufficient safety guarantees. Prior studies report that LLMs systematically underestimate the sodium content of meals, and that lay users assisted by LLMs perform no better than a control group in assessing medical conditions. This paper proposes NutriAgent, a grounded conversational agent for real-time dietary decisions that integrates wearable Internet of Things (IoT) data, clinical nutrition rules, and LLMs. The architecture assigns safety and numerical accuracy to deterministic rules and assigns language understanding and generation to the LLM. A clinical and allergen hard-constraint gate filters candidate meals before LLM ranking; numerical values in generated replies are verified against the database and replaced by deterministic templates upon mismatch; and the system degrades to an offline engine when the LLM is unavailable. Preliminary functional tests on synthetic user profiles yield 0% clinical and allergen violation rates and a calorie mean absolute error of 114.48 kcal.

***Index Terms*—Large language models, conversational agents, health-aware recommender systems, clinical decision support, wearable IoT, grounded generation.**

---

## 四、外部參考來源（待補為正式文獻 [33] 起）

> 正式引用時請改用一手來源，並統一腎病數字的年份與出處。

| 主題 | 一手來源 | 查證連結 |
| --- | --- | --- |
| 溴化鈉中毒病例 | Annals of Internal Medicine: Clinical Cases (2025) | https://www.foxnews.com/health/chatgpt-dietary-advice-sends-man-hospital-dangerous-chemical-poisoning |
| ECRI 2026 十大醫療科技危害 | ECRI Top 10 Health Technology Hazards 2026 | https://home.ecri.org/blogs/ecri-news/examining-the-greatest-health-technology-threats-of-2026 |
| ChatGPT 健康使用規模 | OpenAI, "AI as a Healthcare Ally" (2026/1) | https://www.medicaleconomics.com/view/40-million-people-now-use-chatgpt-daily-for-health-questions-openai-report-finds |
| ChatGPT Health | OpenAI (2026/1/7) | https://www.medicaleconomics.com/view/openai-launches-chatgpt-health-directly-linking-patient-portals-to-the-ai-chatbot |
| LLM 醫療助理 RCT | Bean et al., Nature Medicine 32(2):609–615 (2026), doi:10.1038/s41591-025-04074-y | https://pmc.ncbi.nlm.nih.gov/articles/PMC12920132/ |
| Google Health Coach | Google (2026/5) | https://techcrunch.com/2026/05/07/googles-9-99-per-month-ai-health-coach-launches-may-19/ |
| FoodGuardBench | Luo et al., arXiv:2604.01444 (2026) | https://arxiv.org/html/2604.01444v1 |
| 國人鈉攝取、紅黃綠標示 | 國健署國民營養調查；食藥署 (2026/4) | https://health.tvbs.com.tw/nutrition/362259 |
| 國健署 2026 健康飲食方案 | 國民健康署 (2026) | https://health.ltn.com.tw/article/breakingnews/5404478 |
| 慢性腎臟病盛行率與認知率 | 2026 國家慢性腎臟病防治政策建言書 | https://health.ltn.com.tw/article/breakingnews/5459312 |
| 末期腎病發生率與盛行率 | 厚生基金會引 2022 統計 | https://udn.com/news/story/7266/9542803 |
| 腎病醫療費用、新增透析年齡 | 健保署；2024 台灣腎病年報 | https://www.nownews.com/news/6890615 |
