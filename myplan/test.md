# NutriAgent: Three Core Innovations

本研究提出 **NutriAgent**：一套結合穿戴式物聯網、臨床營養規則與大型語言模型的接地式對話外食決策代理人，旨在突破通用聊天機器人營養數值不穩定、缺乏臨床安全約束、且無法掌握點餐當下情境的限制。

本系統具備三大核心創新：

### 1. Rule-First Safety Gate — 規則優先的臨床安全閘門

依據血壓、血糖、尿酸、LDL-C、eGFR 等健檢指標，以及 TFDA 11 類過敏原，建立確定性的臨床硬約束閘門。候選餐點先通過閘門，LLM 才會進場，確保任何模型輸出都無法讓不安全的餐點出現在推薦中。

### 2. Grounded & Resilient Generation — 接地生成與韌性降級

LLM 只負責理解意圖與生成回覆，所有營養數值一律來自資料庫。回覆中的數字逐一與資料庫比對，不一致即改用確定性模板；LLM 逾時或失效時，自動降級為離線引擎，安全性不受影響。

### 3. IoT-Driven Context Awareness — 物聯網驅動情境感知

整合穿戴裝置活動量、用餐時段、地理位置與歷史回饋，計算帶誤差意識的動態熱量預算，並從步行可達的餐點中，以本地偏好與 LLM 語義評分混合排序出 Top-3。使用者的回饋會即時更新偏好權重，形成 Human-in-the-Loop 閉環。

---

**核心研究價值：** 從流暢但未經驗證的通用 AI 飲食建議（Fluent but Unverified Dietary Advice），邁向安全由架構保證、數值可驗證的情境感知外食決策（Safety-by-Design, Grounded Dietary Decision-Making）。

> 對照簡報 p.11 的五項創新：「閘門先於 LLM」對應第 1 點；「數值接地與驗證備援」「離線韌性」併入第 2 點；「可解釋的混合排序」「帶誤差意識的 IoT 預算」併入第 3 點。
