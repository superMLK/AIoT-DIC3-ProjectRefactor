# NutriAgent Proposal 簡報文字擷取

> 來源：`NutriAgent_Proposal_ppt_第10組.pptx`（25 頁）
> 擷取方式：逐頁擷取標題、內文、表格與講者備忘稿（Notes），移除圖片與連接線等非文字元素，不改寫內容。
> 注意：p.16 架構圖與 p.17 流程圖由大量圖形組成，擷取後的文字順序不代表閱讀順序，請搭配備忘稿理解。各頁末尾的單獨數字為頁碼。

---

## Slide 1
物聯網應用與資料分析（IoT Applications and Data Analysis）｜期末專題 Proposal
# NutriAgent
結合穿戴式 IoT、臨床營養規則與大型語言模型之對話式外食決策代理人
A Grounded Conversational LLM Agent for Real-Time Dietary Decisions Integrating Wearable IoT and Clinical Nutrition Rules
Domain AI Agent = AI Agent + Domain Knowledge + Data Analytics + Real-World Application
組別／成員：第10組 李偉聖、陳建均
指導老師：陳煥
2026 年 11 月

### Notes:
各位老師、同學好，我們這組的期末專題是 NutriAgent。它是一個對話式的外食決策代理人，結合穿戴式 IoT 的活動資料、臨床營養規則，以及 Google Gemini 大型語言模型。整個設計的核心理念只有一句話：規則管安全與數字，LLM 管理解與表達。接下來大約二十分鐘，我會先用報告大綱說明整份簡報的敘事主線。

---

## Slide 2
報告大綱｜Outline
# 六段敘事主線，帶出八個章節
01
02
03
04
05
06
Attention Getter
But
Cure
Development
Experiments
Findings
挑戰與限制
提出解方
方法實作
實驗驗證
預期發現
引起注意
Ch.2
問題與既有限制
p.5–6
Ch.4
Agent Design
p.11–15
Ch.5
System Architecture
p.16
Ch.8
實驗設計
p.22
Ch.8
預期成果、原型現況與結論
p.23–25
Ch.1
研究動機
p.3–4
Ch.3
相關研究與 Research Gap
p.7–10
Ch.6
Flowchart
p.17
Ch.7
Pseudocode
p.18–21
2

### Notes:
這份報告依照 Abstract Story 的六個階段展開。先用 Attention Getter 說明外食與慢性病的現況，再用 But 指出 LLM 飲食建議的數字問題和既有方法的限制，並整理相關研究，收斂出 Research Gap。接著在 Cure 提出 NutriAgent 的 Agent Design，Development 用架構圖、流程圖和四段 Pseudocode 說明怎麼實作。最後是實驗設計和預期成果。我們先從研究動機開始。

---

## Slide 3
研究動機｜Attention Getter
# 外食是日常，慢性病風險也不只屬於長者
近 7 成
每 10 人有 3 人
14 億人
國人有外食習慣；逾 4 成上班族天天外食
20 歲以上國人中，約有這個比例符合代謝症候群
全球罹患高血壓（2024），獲得控制者僅略高於 1/5
調查時間 2025/11
22–65 歲上班族 N = 1,000
[18]
[17]
[15]
越來越多人直接問聊天機器人「這個便當我可以吃嗎？」，LLM 已成為取得飲食建議的新入口。
3

### Notes:
先看三個數字。台灣近七成的人有外食習慣，超過四成上班族天天外食；20 歲以上國人大約每 10 人就有 3 人符合代謝症候群；全球有 14 億人罹患高血壓，但控制得好的只略高於五分之一。也就是說，飲食相關的慢性病風險，其實就在我們身邊、在每天的午餐裡。現在很多人會直接問聊天機器人「這個便當我可以吃嗎」，LLM 已經變成新的飲食建議入口。問題是，它給的數字可靠嗎？

---

## Slide 4
研究動機｜Attention Getter
# LLM 說得通順，但數字不一定可靠
鈉被低估
腎病飲食不合規
CV 20–70%
沒有一個完全符合指引
8 款 7-Eleven 即食便當、5 款聊天機器人：所有模型都低估鈉，重複詢問的變異係數 20–70%
ChatGPT、Copilot、Gemini 生成的腎病飲食；部分輸出的鉀超出安全上限
[5]
[30]
排序有偏誤
穿戴熱量不準
位置會影響排名
MAPE > 30%
LLM 當推薦排序器時，結果會受候選在提示中的位置影響
所有品牌的活動熱量估計，誤差都超過這個水準
[27]
[10]
對高血壓或腎臟病患者來說，這些誤差就是風險。
4

### Notes:
答案是：不一定。一篇研究拿 8 款 7-Eleven 便當去問 5 款聊天機器人，所有模型都低估了鈉，同一個問題重複問，變異係數可以到 20% 到 70%。另一篇請 ChatGPT、Copilot、Gemini 設計腎病飲食，沒有一個完全符合指引，有些甚至鉀超標。LLM 當排序器時還會受候選順序影響，而穿戴裝置的活動熱量誤差也都超過 30%。對一般人這只是誤差，但對高血壓或腎臟病患者，這就是風險。所以下一頁，我們先把問題定義清楚。

---

## Slide 5
問題定義與既有限制｜But
# 問題定義：用對話，在幾秒內給出安全、數字正確的 Top-3
輸入
輸出
自然語言訊息 M、健檢指標 H、過敏原 A、位置 ℓ、用餐時段 t、今日活動量 x
從步行可達的餐點中選出 Top-3，並以自然語言回覆
四個必須同時成立的條件
① 安全
② 數值接地
每份都滿足臨床與過敏原硬約束；LLM 的任何輸出都不能改變這一點
熱量、蛋白質、鈉、價格、距離必須與資料庫一致
③ 適配與個人化
④ 韌性與即時
接近當餐預算，聽得懂「想吃清淡」「太油了」
LLM 無法連線時仍能給出安全推薦
5

### Notes:
我們把問題定義成一個輸入輸出的任務。輸入是使用者的一句話，加上健檢指標、過敏原、位置、用餐時段和今天的活動量；輸出是從步行可達的餐點中挑出 Top-3，並用自然語言回覆。這個輸出要同時滿足四個條件：安全是硬約束，LLM 不能改變；數字必須和資料庫一致；要貼近預算、聽得懂口語偏好；LLM 斷線時也要能給出安全推薦。下一頁看看現有的方法做到哪些。

---

## Slide 6
問題定義與既有限制｜But
# 既有方法各缺一角

| 方法類別 | 代表例 | 主要不足 |
| --- | --- | --- |
| 飲食記錄 App | MyFitnessPal 等 | 事後手動記錄，不會在點餐當下建議；不讀健檢指標 |
| 通用 LLM 聊天機器人 | ChatGPT、Gemini、Claude | 便當鈉低估、CV 20–70% [5]；腎病飲食鉀超標 [30]；不接即時活動與位置 |
| LLM 推薦排序 | Zero-shot LLM ranker [27] | 有位置與熱門偏誤；沒有安全約束的概念 |
| 學術健康感知推薦 | MOPI-HFRS [1]、MORL-A2C [2]、KERL [3] | 健康是可交換的軟性目標；離線；多為食譜 |
| 外送／地圖平台 | 外送 App、地圖搜尋 | 依商業指標排序，不考慮病況與過敏 |

沒有一種方法同時兼顧：點餐當下的即時情境、臨床硬約束、可靠的數字，以及自然的對話互動。
6

### Notes:
現有方法大致分五類。飲食記錄 App 是事後記錄，不會在點餐當下給建議；通用聊天機器人說得流暢，但就像前面看到的，數字不可靠，也不知道你今天走了多少路、人在哪裡。LLM 排序器有偏誤，也沒有安全約束；學術上的健康推薦把健康當成可以交換的分數，而且多半是離線的食譜推薦；外送平台則是依商業指標排序。每一類都缺一角，所以接下來我們更仔細地看相關研究。

---

## Slide 7
相關研究｜Related Work
# 相關研究（一）：AI 技術
1
LLM 與 Agent：Gemini 2.5 Flash 可控制思考預算 [26]；ReAct 交錯推理與工具呼叫 [28]；RAG 以檢索資料作為生成依據 [29]；Gemini API 支援 JSON 結構化輸出 [32]
MORL-A2C 的取捨 [2]
改進前
MORL-A2C
2
LLM 排序器：具零樣本排序能力，但受位置與熱門偏誤影響，可用打亂順序（bootstrapping）緩解 [27]
H-Score@20（健康）
前
46.05%
3
健康感知推薦：MORL-A2C 讓 H-Score@20 從 46.05% → 69.57%，Recall@20 從 25.64% → 23.61% [2]，健康成了可交換的目標
後
69.57%
Recall@20（準確）
4
LLM＋知識：KERL 以知識圖譜約束 LLM [3]；個人因果圖讓 LLM 降低餐後血糖 iAUC [7]
前
25.64%
5
個人化與安全：PEARL 情境式拉霸組第一個月每日比對照組多 296 步 [6]；OGSRL 以約束而非獎勵保證醫療決策安全 [8]
後
23.61%
0%
20%
40%
60%
80%
健康分數大幅上升，Recall 卻小幅下降：健康成了可交換的目標。
LLM 需要結構化依據，安全不能只靠提示詞。
7

### Notes:
AI 技術面有五條線。第一，Gemini 2.5 Flash、ReAct、RAG 和結構化輸出，讓 LLM 能當 Agent 的大腦並呼叫工具。第二，LLM 可以零樣本排序，但會受位置和熱門偏誤影響。第三，右邊這張圖是 MORL-A2C：健康分數從 46% 拉到將近 70%，但 Recall 從 25.64% 掉到 23.61%，代表健康被當成可以拿來交換準確度的目標。第四、第五，知識圖譜、因果圖和安全強化學習都在告訴我們：LLM 需要結構化依據，安全不能只靠提示詞。接著看領域知識。

---

## Slide 8
相關研究｜Related Work
# 相關研究（二）：領域知識
高血壓
糖尿病前期
痛風
2025 AHA/ACC 建議鈉 < 2,300 mg/日，理想 < 1,500 mg；以 DASH 為第一線 [12]
ADA 2026 以醫學營養治療為核心 [13]
2020 ACR 指引條件式建議限制普林 [14]
WHO 建議 < 2,000 mg/日 [15]
慢性腎臟病
過敏原
能量與依從性
KDIGO 2024 建議鈉 < 2 g/日；鉀應依腎功能與血鉀個別化 [31]
TFDA 11 類強制標示（107 年公告、109/7 施行）[16]
穿戴熱量誤差 > 30% [10][11]；BMR 採 Mifflin-St Jeor [22]
DIETFITS 顯示依從性比飲食理論更關鍵 [20]
註：單餐 700 mg 鈉門檻 = 每日 2,000 mg ÷ 3 餐 ≈ 667 mg 取整，屬設計決策，不是指引原文。
8

### Notes:
領域知識是我們規則庫的來源。高血壓依 2025 AHA/ACC 指引，每日鈉建議低於 2,300 毫克、理想低於 1,500；WHO 則建議低於 2,000。糖尿病前期、痛風、慢性腎臟病和過敏原，也都各有指引或法規依據，例如台灣的 11 類過敏原強制標示。綠色這格提醒我們，穿戴熱量誤差大，而且依從性比飲食理論更重要。要特別說明的是，單餐 700 毫克鈉的門檻，是我們把每日 2,000 除以三餐後取整的設計決策，不是指引原文。下一頁把重要文獻整理成一張表。

---

## Slide 9
相關研究｜Related Work　（灰底三列為領域知識文獻）
# Article Summary Table

| Ref. | Year | Problem | AI Method | Dataset | Findings | Limitation |
| --- | --- | --- | --- | --- | --- | --- |
| [1] | 2025 | 健康推薦缺個人化與可解釋性 | 圖學習＋Pareto＋LLM 解釋 | 自建 2 個基準 | 三目標皆優於基準 | 健康為軟性目標；離線 |
| [2] | 2026 | 靜態權衡無法處理序列決策 | A2C RL 重排序 | MOPI-HFRS 基準 | H-Score 46.05→69.57% | Recall 下降，無安全保證 |
| [3] | 2025 | 食物 KG 與 LLM 未整合 | KG 子圖＋LLM | 自建食譜問答 | 顯著優於既有方法 | 居家食譜，非可購餐點 |
| [4] | 2025 | LLM 估營養是否可靠 | 12 LLM；CoT、RAG | NutriBench 11,857 筆 | 接近營養師且更快 | 只估算不推薦 |
| [5] | 2025 | AI 判讀台灣便當營養 | 5 款聊天機器人 vs 營養師 | 8 款 7-Eleven 便當 | 鈉普遍低估，CV 20–70% | 樣本僅 8 份 |
| [6] | 2025 | 個人化運動提醒 | Contextual Bandit | 13,463 人 RCT | +296 步／日 | 運動非飲食 |
| [7] | 2025 | LLM 個人化推理 | 個人因果圖＋LLM | 個人縱向資料 | 降低餐後血糖 iAUC | 需長期資料 |
| [8] | 2025 | 醫療 RL 安全性 | 守門式安全 RL | 離線臨床資料 | 約束內逼近最佳策略 | 治療非飲食 |
| [9] | 2025 | 影像營養估算 | 8 個多模態 LLM | Nutrition5k、MetaFood3D | 熱量 MAPE 51%→29% | 油脂醬汁誤差大 |
| [27] | 2024 | LLM 能否當排序器 | Zero-shot ranking | 公開序列推薦資料 | 有零樣本能力；受位置偏誤影響 | 非健康領域 |
| [30] | 2026 | LLM 腎病飲食是否合規 | ChatGPT-4o、Copilot、Gemini | 標準化情境＋專家評分 | 部分鉀超標；無模型完全合規 | 情境有限 |
| [10] | 2022 | 腕戴裝置準確度 | 系統性回顧 | 已發表驗證研究 | 能量消耗 MAPE > 30% | 異質性高 |
| [12] | 2025 | 高血壓管理 | —（臨床指引） | 證據回顧 | 鈉 < 2,300、理想 < 1,500 mg | 未定單餐門檻 |
| [31] | 2024 | 慢性腎臟病管理 | —（臨床指引） | 證據回顧 | 鉀依腎功能個別化 | 難轉為單一規則 |

9

### Notes:
這張表整理了 14 篇關鍵文獻，上面 11 篇是 AI 技術，下面灰底 3 篇是領域知識。我不會逐篇念，請大家看最右邊的 Limitation 欄：健康常被當成軟性目標、多數是離線系統、只做估算不做推薦，或者情境有限。另外像 [5] 和 [30] 直接顯示 LLM 在鈉與鉀上的數字問題，[12] 和 [31] 則說明臨床指引很難直接轉成單一規則。把這些限制收斂起來，就是下一頁的 Research Gap。

---

## Slide 10
相關研究｜Research Gap
# Research Gap：缺的是一個讓規則與 LLM 分工的外食 Agent
Existing Research
Limitations
Our Project
Research Gap
NutriAgent
LLM 能理解、排序與給建議 [4][26][27]
① LLM 數值不穩，鈉與鉀最危險 [5][30]
缺少一個讓規則負責安全與數字、LLM 負責理解與表達，結合即時活動量與步行可達餐點，且 LLM 失效時仍安全運作的外食決策 Agent
① 意圖解析
LLM＋知識可處理飲食限制 [3][7]
② LLM 排序有偏誤 [27]
② 確定性臨床閘門
③ 健康被當可交換目標 [2]
③ 本地偏好＋Gemini 混合排序
健康推薦兼顧口味與健康 [1][2]
④ 忽略位置與可購性 [1][3]
⑤ 穿戴熱量誤差大 [10][11]
個人化有大規模實證 [6]
④ 接地回覆與數值驗證
⑤ 回饋閉環
10

### Notes:
把前面的研究從左到右串起來。既有研究告訴我們，LLM 能理解、能排序，也能結合知識處理飲食限制。但它有五個限制：數值不穩、排序有偏誤、健康被當成可交換的目標、忽略位置與可購性，以及穿戴熱量誤差大。所以我們的 Research Gap 是：缺少一個讓規則負責安全與數字、LLM 負責理解與表達，而且 LLM 失效時仍能安全運作的外食決策 Agent。最右邊就是我們的回答 NutriAgent，下一頁正式介紹它。

---

## Slide 11
Proposed Solution｜Cure
# NutriAgent：用聊天的方式，得到安全又數字正確的推薦
定義：結合臨床營養規則、穿戴式 IoT 資料與 Google Gemini 的接地式對話外食推薦 Agent
情境故事
五項創新
陳先生，45 歲專案經理｜第一期高血壓（收縮壓 138 mmHg）、LDL-C 偏高、海鮮過敏
1　閘門先於 LLM
陳先生：中午在公館，剛通勤走了不少路，想吃清淡一點
2　數值接地與驗證備援
NutriAgent：回覆 3 個步行 5 分鐘內的選項，每份都寫出與資料庫一致的數字，例如「鈉 580 mg，低於 700 mg 警戒線」
3　可解釋的混合排序
4　帶誤差意識的 IoT 預算
陳先生：太油了，換一家
5　離線韌性
NutriAgent：先調降重口味權重，再重新推薦
11

### Notes:
NutriAgent 是一個結合臨床營養規則、穿戴資料和 Google Gemini 的接地式對話推薦 Agent。用一個情境說明：45 歲的陳先生有第一期高血壓、LDL 偏高、對海鮮過敏，他打字說中午在公館、剛走了不少路、想吃清淡一點。系統回覆三個步行五分鐘內的選項，每個數字都和資料庫一致；他說太油了，系統就先調降重口味權重再重新推薦。右邊是五項創新，顏色對應後面各頁的語意：紅色是安全、橘色是 LLM、綠色是資料、藍色是輸入輸出。接下來四頁說明 Agent Design。

---

## Slide 12
Agent Design（一）｜Cure
# 規則管安全與數字，LLM 管理解與表達
LLM：理解與表達
確定性規則：安全與數字

| 功能 | LLM 的任務 | 限制與防護 |
| --- | --- | --- |
| 意圖與情境理解 | 原型為關鍵字規則；規劃改由 Gemini 以 JSON schema 輸出 | 不得移除已宣告的過敏原；須通過 schema 驗證 |
| 語義評分 | 對安全池每道餐點給 0–1 分（JSON 模式，temperature 0.1） | 只看得到安全池；只占 q 的一半 |
| 回覆生成 | 依資料 JSON 與最近 4 輪對話生成回覆（temperature 0.25） | 數字逐字引用；不一致改用模板 |
| 安全判斷 | 不由 LLM 負責 | 完全由確定性規則決定 |
| 營養數值 | 不由 LLM 產生 | 只來自資料庫與運算結果 |

12

### Notes:
這頁是整個設計的核心分工。LLM 負責三件事：理解意圖與情境、對安全池裡的餐點做語義評分、以及生成回覆，而且每件事都有防護，例如評分只看得到安全池、回覆的數字必須逐字引用。紅色這兩列是 LLM 完全碰不到的：安全判斷由確定性規則決定，營養數值只來自資料庫與運算結果。這樣就算 LLM 出錯，也不會讓不安全的餐點出現，或讓數字被改掉。下一頁看 Planner 怎麼調度這些工具。

---

## Slide 13
Agent Design（二）｜Cure
# Planner 負責調度，混合排序負責挑出 Top-3
Planner 的職責
排序設計
本地偏好分數 p：口味權重 w = [清淡, 重口味, 高蛋白] × 餐點風味、蛋白質密度、熱量接近度、關鍵字加分
意圖解析：recommend／feedback／explain_violation
情境覆寫：從對話抽出地點、時段、活動；過敏原只增不減
Gemini 語義分數 g：0–1（候選順序打亂）
回饋意圖先執行偏好更新，再重新推薦
q = 0.5 · p + 0.5 · g
無 Gemini 時 q = p
工具調度：IoT Adapter → LBS → 臨床閘門 → 混合排序 → 接地回覆
Score = 0.50 · q + 0.35 · NutriFit − 0.15 · Dist
產生推理軌跡（Reasoning Trace），顯示本次使用 Gemini 或離線引擎
採納 +1.0
太油 −0.6
跳過 −0.2
回饋獎勵
13

### Notes:
左邊是 Planner，也就是 Agent 的中樞。它先判斷這句話是要推薦、是回饋，還是在問為什麼不能吃；再從對話中抽出地點、時段和活動覆寫設定，過敏原只能增加不能減少。如果是回饋，就先更新偏好再重新推薦。右邊是排序：本地偏好分數 p 和 Gemini 語義分數 g 各占一半組成 q，沒有 Gemini 時 q 就等於 p；最後再和營養適配度、距離加權成總分。使用者的回饋會轉成獎勵，用來更新口味權重。下一頁說明排序之前的兩道關卡。

---

## Slide 14
Agent Design（三）｜Cure
# 先算預算，再過閘門
動態熱量預算
合理性檢查：步數 > 10,000 但平均心率 ≤ 70 bpm → 判定為行車震動假訊號並校正
穿戴資料失敗 → 靜態估計＋標記 LOW_CONF
動態 TDEE = BMR（Mifflin-St Jeor）+ 0.65 × 活動熱量
單餐上限 900 kcal；時段比例：早 25%／午 40%／晚 35%
臨床硬閘門：候選餐點 → 紅色閘門 → 安全池
臨床硬閘門（不可違反）
收縮壓 ≥ 130 或舒張壓 ≥ 85 → 排除鈉 > 700 mg 或油炸
空腹血糖 ≥ 100 → 排除高 GI 或添加糖 > 5 g
安全池為空 →
安全池 S
尿酸 ≥ 7.0 → 排除高普林
不放寬規則
候選餐點 C
LDL-C ≥ 130 → 排除飽和脂肪 > 5 g 或油炸
只有這裡的餐點會進入排序與 LLM
步行可達的 LBS 檢索結果
回報 NO_COMPLIANT_MEAL＋點餐調整建議
eGFR < 60 → 排除高鉀或高磷
過敏原 → TFDA 11 類別名展開（「海鮮」= 魚類＋甲殼類）
被排除的餐點（灰）→ 記入拒絕紀錄 V，附排除原因
14

### Notes:
排序之前有兩道關卡。第一道是熱量預算：用 Mifflin-St Jeor 算基礎代謝，活動熱量只加 0.65 倍，因為穿戴裝置誤差大；單餐上限 900 大卡，再依早午晚比例分配。如果步數破萬但心率很低，就判定是坐車震動造成的假訊號；穿戴資料拿不到時，改用靜態估計並標記低信心。第二道是臨床硬閘門，依血壓、血糖、尿酸、LDL、eGFR 和過敏原逐條排除餐點，被排除的會記錄原因。如果最後安全池是空的，我們不放寬規則，而是誠實回報並給點餐調整建議。下一頁看 LLM 這一側的防護。

---

## Slide 15
Agent Design（四）｜Cure
# 接地、驗證與韌性：LLM 出錯或斷線，安全都不受影響
提示結構
數值驗證
system instruction：角色＋「數字必須逐字引用」
回覆中的數字必須與資料庫一致，不一致就改用確定性模板（Algorithm 3 第 15 行）
資料 JSON：活動、預算、閘門摘要、Top-3、點餐建議
最近 4 輪對話＋最新訊息
降級
隱私
沒有金鑰、逾時 12 秒或 API 錯誤 → 自動改用離線引擎，安全性不受影響
啟用 Gemini 時健檢資料會送到雲端；正式版改為只送衍生旗標、明確告知並取得同意；金鑰改用 x-goog-api-key header 傳遞
15

### Notes:
LLM 這一側有四道防護。提示詞由三部分組成：系統指令要求數字逐字引用，資料 JSON 提供所有可以引用的數字，再加上最近四輪對話和最新訊息。回覆生成後，我們會檢查裡面的數字是否都和資料庫一致，不一致就改用確定性模板。沒有金鑰、逾時 12 秒或 API 出錯時，系統自動降級到離線引擎，安全性完全不受影響。最後是隱私：目前啟用 Gemini 時健檢資料會送到雲端，正式版會改成只送衍生旗標並取得同意。接下來把這些設計放進系統架構圖。

---

## Slide 16
System Architecture｜Development
實線：資料流
# 8 個模組＋LLM 服務，規則在前、語言在後
紫虛線：Planner 調度
橘虛線：LLM 呼叫與閉環回饋
① User / Application Scenario
外食族在對話介面直接提問：「中午在公館想吃清淡的」
穿戴裝置
Health Connect／HealthKit
（原型為活動模擬器）
② Query / Input
自然語言訊息＋側欄：健檢檔案・過敏原・位置・活動
健檢報告
血壓、血糖、尿酸、LDL-C、eGFR
③ Planner & Reasoning Agent
意圖解析・情境覆寫・工具調度・例外處理・推理軌跡
K1 外食營養資料庫
④ Data Acquisition / Retrieval
GPS／商圈
超商・便當營養標示、座標・價格・過敏原
經緯度座標
IoT Adapter＋合理性檢查・Haversine 檢索 1.2→2.5 km
⑤ Analytics Engine
動態 TDEE・單餐上限 900 kcal・8 維狀態 s_t
K2 臨床規則庫
③ 調度
鈉 > 700 mg・高 GI・高普林・飽和脂肪 > 5 g・高鉀高磷
⑥ Domain Knowledge Integration
臨床硬閘門＋TFDA 11 類過敏原＋點餐調整建議
K3 過敏原別名表
TFDA 11 類＋海鮮複合類
⑦ Decision / Recommendation
JSON 評分
LLM 服務
本地偏好＋Gemini 語義評分（只看安全池）→ Top-3
K4 互動記憶 Buffer
Google Gemini API
(s, a, r, s′, f)／偏好權重 w
gemini-2.5-flash
⑧ Output & Interaction
無金鑰時離線備援
接地回覆＋數值驗證・推理軌跡・餐點卡片・回饋更新
接地回覆
回饋 r → 更新 w
16

### Notes:
這是系統架構。中間由上到下是八個模組：使用者提問、輸入、Planner、資料取得、分析引擎、領域知識閘門、推薦排序，最後是輸出與互動。左邊是外部資料：穿戴裝置、健檢報告和 GPS，以及左下角的 Gemini 服務；注意 Gemini 只連到第七和第八個模組，也就是只負責評分和回覆，排在臨床閘門之後，這就是「規則在前、語言在後」。右邊是四個知識庫，K4 互動記憶接收第八個模組的回饋，再更新偏好權重。原型對應：① app.py、② /api/chat 與 /api/config、③ agent.py chat()、④ iot_pipeline.py 與 spatial_lbs.py、⑤ encode_state()、⑥ clinical_rules.py、⑦ rank_meals()、⑧ generate_conversational_reply() 與 update_online()。下一頁用流程圖看一則訊息怎麼走完這些模組。

---

## Slide 17
Flowchart｜Development
# Flowchart：從一則訊息到下一則訊息
不放寬規則
NO_COMPLIANT_MEAL
＋點餐調整建議
開始：使用者在對話框輸入訊息
是
安全池為空？
非正常路徑
回饋意圖 → 先更新偏好
否
Input：自然語言訊息＋側欄設定
Gemini 語義評分
只送安全池、順序打亂
回傳 JSON 0–1
是
穿戴失敗 → 靜態估計＋LOW_CONF
Gemini 可用？
Planner：意圖解析＋情境覆寫
過敏原只增不減
否
候選 < 4 → 擴大至 2.5 km
混合排序 → Top-3
q = 0.5·p + 0.5·g
是
Algorithm 4
先更新偏好權重 w
回饋意圖？
安全池為空 → 不放寬規則
接地回覆生成
Gemini 逐字引用；無則模板
否
否
例外降級：靜態估計
＋標記 LOW_CONF
穿戴資料成功？
Gemini 不可用 → 只用本地偏好
否
改用模板回覆
保證數字與資料庫一致
數字 = 資料庫值？
是
合理性檢查 → 動態 TDEE
當餐預算（上限 900 kcal）
數字不一致 → 改用模板回覆
是
Output
回覆＋推理軌跡＋Top-3 卡片
空間檢索：Haversine 1.2 km
無
逾時無回饋
不更新偏好
使用者回饋？
否
擴大半徑
至 2.5 km（一次）
候選數 ≥ 4？
有
灰色菱形＝判斷
藍色虛線＝Human-in-the-Loop 回到輸入
是
Learning：Algorithm 4 更新 w
採納 +1.0／太油 −0.6
跳過 −0.2 → 存入互動記憶
Human-in-the-Loop：
下一則訊息使用更新後偏好
Domain Gate
臨床硬約束＋TFDA 11 類
17

### Notes:
流程圖從使用者輸入一則訊息開始，左欄往下走，再接到右欄。Planner 先解析意圖，如果是回饋就先用 Algorithm 4 更新偏好；接著取穿戴資料，失敗就改用靜態估計並標記低信心；算完預算後在 1.2 公里內檢索，候選不到 4 個就擴大到 2.5 公里一次，然後通過臨床閘門。右欄：安全池為空就不放寬規則，直接回報；Gemini 可用才做語義評分，再混合排序、生成接地回覆，數字對不上就改用模板。輸出後如果使用者給回饋，就更新偏好並存入互動記憶，藍色虛線表示下一則訊息會用上更新後的偏好。右側欄整理了這六條非正常路徑，接下來用四段 Pseudocode 把它寫成演算法。

---

## Slide 18
Pseudocode｜Development
# Algorithm 1：對話 Agent 主流程
重點行
Algorithm 1: NutriAgent Conversational Agent Loop
Input : message M, profile U, metrics H, allergens A, sidebar context (L, t, act),
第 01–05 行｜Planner
        history Hist, preference weights w
意圖解析、情境覆寫；過敏原只增不減；回饋先更新偏好
Output: reply Y, Top-3 recommendations R, reasoning trace T
01: I <- parse_intent(M)              # recommend / feedback / explain_violation
02: (L, t, act) <- override_context(I, L, t, act)   # place, meal time, activity
第 07 行｜例外
03: A <- union(A, I.allergens)        # chat may add allergens, never remove
穿戴資料失敗 → 靜態估計＋LOW_CONF
04: if I.intent == feedback and last_R exists then
05:     w <- PreferenceUpdate(last_s, last_R[1], I.feedback)   # Algorithm 4
06: try: telemetry <- IoTAdapter.fetch(act)       # Health Connect / simulator
第 13–14 行｜閘門
07: catch timeout/denied: telemetry <- static_estimate(U); flag LOW_CONF
08: telemetry <- plausibility_check(telemetry)    # steps>10000 and HR<=70
安全池為空就回報，不放寬規則
09: TDEE <- MifflinStJeor(U) + 0.65 * telemetry.active_kcal
10: budget <- min(TDEE * ratio[t], 900); macros <- split(budget, H)
第 16 行｜LLM
11: C <- LBS.retrieve(L, 1.2 km); if |C| < 4 then C <- LBS.retrieve(L, 2.5 km)
LLM 只在閘門之後才進場
12: S, V <- ClinicalGate(C, H, A)                 # Algorithm 2
13: if S is empty then
14:     return compose(NO_COMPLIANT_MEAL, order_tips(C, H)), [], T  # never relax
15: s_t <- encode_state(t, budget, steps, H, w)   # 8-dim state
16: R, Y <- HybridRankAndReply(S, s_t, budget, M, V, Hist)   # Algorithm 3
17: T <- trace(I, telemetry, budget, |C|, |V|, |S|, engine_used)
18: Hist.append(M, Y); return Y, R, T
18

### Notes:
Algorithm 1 是整個 Agent 的主迴圈，也就是剛剛流程圖的程式版。紫色的 1 到 5 行是 Planner：解析意圖、覆寫情境，第 3 行保證對話只能新增過敏原，第 4、5 行在回饋時先呼叫 Algorithm 4 更新偏好。第 7 行是例外處理，穿戴資料拿不到就用靜態估計並標記低信心。第 13、14 行是關鍵：安全池為空就直接回報，絕不放寬規則；而 LLM 要到第 16 行、通過閘門之後才進場。下一頁展開第 12 行呼叫的臨床閘門。

---

## Slide 19
Pseudocode｜Development
# Algorithm 2：臨床與過敏原硬約束閘門
Algorithm 2: Clinical and Allergen Hard-Constraint Gate
可稽核的規則
Input : candidate meals C, health metrics H, declared allergens A
每條規則都可單元測試；排除原因會記錄下來，供稽核與回答「為什麼不能吃」
Output: safe pool S and rejection log V
01: S <- [], V <- []
02: aliases <- union of ALIAS_MAP[a] for a in A   # TFDA-11; seafood -> fish+crustacean
03: for each meal m in C do
04:     reasons <- []
排除原因代碼
05:     if m.allergens intersects aliases then reasons.add(ALLERGEN)
06:     if H.SBP >= 130 or H.DBP >= 85 then
ALLERGEN・HTN
PREDIABETES・GOUT
LDL・CKD
07:         if m.sodium_mg > 700 or m.is_deep_fried then reasons.add(HTN)
08:     if H.fasting_glucose >= 100 then
09:         if m.GI == High or m.added_sugar_g > 5 then reasons.add(PREDIABETES)
10:     if H.uric_acid >= 7.0 and m.purine == High then reasons.add(GOUT)
11:     if H.LDL >= 130 and (m.sat_fat_g > 5 or m.is_deep_fried)
12:         then reasons.add(LDL)
13:     if H.eGFR < 60 and (m.potassium == High or m.phosphorus == High)
14:         then reasons.add(CKD)
15:     if reasons is empty then S.add(m) else V.add(m, reasons)
16: return S, V
19

### Notes:
Algorithm 2 是臨床硬閘門。第 2 行先把使用者宣告的過敏原展開成 TFDA 11 類的別名，例如「海鮮」會展開成魚類加甲殼類。接著對每道候選餐點，依血壓、空腹血糖、尿酸、LDL 和 eGFR 逐條檢查，只要違反任何一條，就把原因記下來。最後沒有任何原因的餐點才進入安全池 S，其他的連同原因放進拒絕紀錄 V。因為每條規則都是確定性的，所以都可以單元測試，也能回答使用者「為什麼這個不能吃」。接下來是 LLM 真正上場的 Algorithm 3。

---

## Slide 20
Pseudocode｜Development
# Algorithm 3：LLM 混合排序與接地回覆
重點行
Algorithm 3: Hybrid LLM Ranking and Grounded Reply
Input : safe pool S, state s_t, budget, message M, rejection log V, history Hist
第 03 行｜LLM 評分
Output: Top-3 R and reply Y
只送安全池，且打亂候選順序
01: g <- {}
02: if LLM.available() and M is not empty then
第 06 行｜混合分數
03:     g <- LLM.json_scores(shuffle(S), w, budget, M)   # 0..1 per meal, temp 0.1
本地偏好與 Gemini 各占一半；沒有 g 時 q = p
04: for each meal m in S do
05:     p <- local_pref(s_t, a_m, M)    # flavor match, protein density, kcal fit
06:     q <- 0.5 * p + 0.5 * g[m] if m in g else p
第 15–16 行｜數值驗證
07:     fit <- 1 - (0.6 * calorie_error + 0.4 * protein_error)
08:     m.score <- 0.50 * q + 0.35 * fit - 0.15 * min(1, m.distance / 1.5)
數值驗證通過，才採用 LLM 回覆
09: R <- top_k(S, by = score, k = 3)
10: E <- template_explain(R, H, budget); assert numbers_in(E) == db_values(R)
11: Y <- template_reply(R, E)                       # deterministic baseline
12: if LLM.available() then
13:     Y_llm <- LLM.generate(rules = 'quote numbers verbatim',
14:                           context = JSON(R, V, budget), history = Hist[-4:])
15:     if Y_llm != null and numbers_in(Y_llm) subset_of db_values(R, budget) then
16:         Y <- Y_llm                  # else keep template (timeout/error/mismatch)
17: return R, Y
20

### Notes:
Algorithm 3 是 LLM 參與的地方，但它被限制得很嚴格。第 3 行只把安全池送給 Gemini，而且先打亂順序，避免位置偏誤；第 6 行把本地偏好和 Gemini 分數各取一半，沒有 Gemini 時就只用本地偏好。第 10、11 行先用模板產生一份確定性的回覆當作底線。第 15、16 行是數值驗證：Gemini 回覆裡的數字必須全部來自資料庫，通過才採用，否則保留模板。最後一段是使用者回饋怎麼更新偏好。

---

## Slide 21
Pseudocode｜Development
# Algorithm 4：線上偏好更新（Human-in-the-Loop）
Algorithm 4: Online Preference Update (Human-in-the-Loop)
設計說明
Input : last state s_t, meal vector a, feedback f in {accept, too_oily, skip}
三維權重以固定步長調整，獎勵與轉移存入互動記憶，未來可離線擬合情境式拉霸
Output: preference weights w = [light, savory, protein], memory buffer D
01: r <- {accept: +1.0, too_oily: -0.6, skip: -0.2}[f]
02: w_old <- w
03: if f == accept then raise w for flavors in a by 0.08 (protein if ratio >= 0.22)
04: if f == too_oily then w.savory <- max(0.1, w.savory - 0.15)
回饋獎勵 r
05:     and w.light <- min(1.0, w.light + 0.12)
06: if f == skip then w <- clip(0.95 * w, 0.1, 1.0)
採納 +1.0
太油 −0.6
跳過 −0.2
07: s_next <- s_t with preference slots replaced by w
08: D.push(s_t, a, r, s_next, f)
09: delta <- mean(|w - w_old|)          # adaptation magnitude, shown in trace
10: return w, D
21

### Notes:
Algorithm 4 是 Human-in-the-Loop 的偏好更新。使用者的回饋只有三種：採納、太油、跳過，分別對應 +1.0、−0.6、−0.2 的獎勵。採納會提高這道餐點相關口味的權重；說太油就降低重口味、提高清淡；跳過則整體小幅縮減。每次的狀態、餐點、獎勵和下一個狀態都存進互動記憶 D，所以未來可以離線擬合情境式拉霸，讓個人化更好。四段演算法講完了，接下來說明怎麼驗證這套設計。

---

## Slide 22
實驗設計｜Experiments
# 實驗設計：五個基準、十項指標，有無 LLM 都要安全
Metrics
Dataset

| 指標 | 目標 |
| --- | --- |
| CVR | 0%（不論是否啟用 LLM） |
| AVR | 0% |
| 數值接地率 | 最終輸出 100%（另量測 Gemini 原始輸出） |
| 意圖正確率 | ≥ 90% |
| 熱量 MAE | ≤ 180 kcal |
| 延遲 | 離線 ≤ 50 ms；Gemini P95 ≤ 5 s |
| 降級率 | 回報實測值 |
| 位置一致性 | 回報實測值 |
| 偏好收斂 | ≤ 5 次 |
| SUS | ≥ 68 |

50 組合成臨床使用者（含 eGFR）
≥ 200 筆餐點（補鉀磷等級）
≥ 200 句中文對話測試集（人工標註意圖與情境）
Baselines
B1　最近距離／熱門
B2　靜態熱量規則
B3　Gemini Zero-Shot（不經閘門）
B4　消融：移除閘門
B5　離線 NutriAgent（不用 LLM）
使用者測試
10–15 人、5 個工作日午餐，分「啟用 Gemini」與「離線模式」兩組
22

### Notes:
實驗會用三份資料：50 組含 eGFR 的合成臨床使用者、至少 200 筆補上鉀磷等級的餐點，以及至少 200 句人工標註的中文對話。比較對象有五個，其中最關鍵的是 B3：讓 Gemini 直接推薦、不經閘門，以及 B4 把閘門拿掉的消融實驗。指標中最重要的是紅色兩列 CVR 和 AVR，也就是臨床規則與過敏原的違規率，目標是 0%，而且不論有沒有啟用 LLM 都一樣；其他還有數值接地率、意圖正確率、熱量誤差、延遲和 SUS 等。最後會找 10 到 15 人做 5 個工作日的午餐實測。下一頁區分預期成果和原型目前的狀態。

---

## Slide 23
預期成果｜Findings
# 預期成果、原型現況與待辦
預期
原型檢核
待辦
Expected Outcomes
原型現況
已知待辦
尚未完成正式實驗，以下為預期，不是結果
測試 9/9 通過；離線模式，未設定 GEMINI_API_KEY
Gemini 回覆尚未做數值驗證（第一優先）
CVR／AVR 維持 0%，不論是否啟用 Gemini
意圖解析仍為關鍵字規則

| 指標 | 原型檢核值 |
| --- | --- |
| CVR／AVR | 0.0%／0.0% |
| 熱量 MAE | 114.48 kcal |
| 平均／P95 延遲 | 0.08 ms／0.18 ms（不含 LLM） |

語義評分尚未打亂順序、未指定 schema
最終回覆數值接地率 100%
API 金鑰需改用 header 傳遞
Gemini Zero-Shot 預期出現非零違規（待驗證的假設）
資料缺少鉀磷與 eGFR 欄位
尚未量測 Gemini 路徑延遲
熱量 MAE ≤ 180 kcal
5 組合成檔案、45 次評估，僅為功能檢核。
23

### Notes:
這頁我刻意把三件事分開。左邊是預期成果，因為正式實驗還沒做，這些都是預期，不是結果；其中「Gemini Zero-Shot 會出現非零違規」是一個待驗證的假設。中間是原型目前的檢核值：在離線模式下 9 項測試全部通過，違規率是 0，熱量 MAE 約 114 大卡，延遲不到 1 毫秒，但這只是 5 組合成檔案、45 次評估的功能檢核。右邊是已知待辦，第一優先是替 Gemini 回覆加上數值驗證，其他包括意圖解析改用 LLM、評分打亂順序、金鑰改用 header 傳遞等。最後做個總結。

---

## Slide 24
結論｜Conclusion
# 結論與貢獻
1
「規則管安全與數字、LLM 管理解與表達」的接地式對話 Agent 架構，含 4 段 Pseudocode
2
可稽核、可單元測試的臨床與 TFDA 11 類過敏原規則庫
3
LLM 輸出的數值驗證與降級機制，以及對應的評估指標
4
台灣外食營養與空間資料集，以及中文外食對話測試集
5
可重現的開源原型（對話式 Web 介面、CLI、測試、OpenSpec）
Q & A
安全由架構保證，語言交給 LLM，數字只來自資料庫。
24

### Notes:
總結我們的五項貢獻：一套規則與 LLM 分工的接地式對話 Agent 架構和四段演算法；可稽核的臨床與過敏原規則庫；LLM 輸出的數值驗證、降級機制與評估指標；台灣外食的資料集與中文對話測試集；以及可重現的開源原型。最後用一句話收尾：安全由架構保證，語言交給 LLM，數字只來自資料庫。以上是我們的提案，參考文獻列在最後一頁，接下來開放大家提問，謝謝。

---

## Slide 25
參考文獻｜References
# References
[1] Zhang, Z., et al. (2025). MOPI-HFRS. KDD ’25, 2860–2871.
[17] 衛生福利部國民健康署。代謝症候群相關新聞稿。
[2] Vasantlal, A., Zolla, J., & Zhang, C. (2026). MORL-A2C. arXiv:2606.23603.
[18] 台灣營養基金會等（2025）。代謝認知與飲食行為調查（N = 1,000）。
[3] Mohbat, F., & Zaki, M. J. (2025). KERL. ACL 2025.
[19] Android Developers. (2025). Google Fit migration FAQ.
[4] Dhaliwal, M. P., et al. (2025). NutriBench. ICLR 2025.
[20] Gardner, C. D., et al. (2018). DIETFITS. JAMA, 319(7), 667–679.
[5] Hsuan, C.-F., et al. (2025). Ready-to-eat meal boxes: nutritionists vs chatbots. Nutrients, 17(19), 3044.
[21] Hall, K. D., et al. (2011). Energy imbalance and bodyweight. The Lancet, 378(9793), 826–837.
[6] Lee, A. A., et al. (2025). PEARL four-arm RCT. arXiv:2508.10060.
[22] Mifflin, M. D., et al. (1990). Predictive equation for resting energy expenditure. AJCN, 51(2), 241–247.
[7] Yang, Z., & Rahmani, A. (2025). Personalized causal graph reasoning for LLMs. arXiv:2503.00134.
[23] Thaler, R. H., & Sunstein, C. R. (2008). Nudge.
[8] Yan, R., et al. (2025). Offline guarded safe RL for medical treatment. NeurIPS 2025.
[24] Lei, H., Tewari, A., & Murphy, S. A. (2017). Actor-critic contextual bandit for mHealth. arXiv:1706.09090.
[9] Bhatambarekar, G. M. (2025). Conversational multimodal LLMs for food nutritional information retrieval. Virginia Tech.
[25] Švihrová, R., et al. (2025). Causal inference and multi-armed bandits in digital health. Frontiers in Digital Health.
[10] Germini, F., et al. (2022). Wrist-wearable activity trackers: systematic review. JMIR, 24(1), e30791.
[26] Gemini Team, Google. (2025). Gemini 2.5 technical report. arXiv:2507.06261.
[11] Shcherbina, A., et al. (2017). Wrist-worn heart rate and energy expenditure accuracy. J Pers Med, 7(2), 3.
[27] Hou, Y., et al. (2024). LLMs are zero-shot rankers for recommender systems. ECIR 2024.
[12] Jones, D. W., et al. (2025). 2025 AHA/ACC high blood pressure guideline.
[28] Yao, S., et al. (2023). ReAct. ICLR 2023.
[13] ADA Professional Practice Committee. (2026). Standards of Care in Diabetes—2026.
[29] Lewis, P., et al. (2020). Retrieval-augmented generation. NeurIPS 2020.
[30] Aydın Çil, M., et al. (2026). AI-generated renal diets by different LLMs. BMC Nephrology, 27(1).
[14] FitzGerald, J. D., et al. (2020). 2020 ACR gout guideline. Arthritis & Rheumatology, 72(6), 879–895.
[31] KDIGO CKD Work Group. (2024). KDIGO 2024 CKD guideline. Kidney International, 105(4S), S117–S314.
[15] WHO. (2025). Global report on hypertension 2025.
[16] 衛生福利部（2018）。食品過敏原標示規定，應標示之過敏原增加為 11 項。
[32] Google AI for Developers. (2026). Gemini API: Structured output.
25

### Notes:
這是本簡報引用的 32 篇參考文獻，編號和前面各頁的引用一一對應。左欄主要是 AI 技術與健康推薦的研究，以及穿戴裝置準確度和臨床指引；右欄包含國內調查、能量平衡、行為經濟學與 LLM 相關的技術文獻。報告到這裡結束，若有同學或老師想追問特定數字的出處，可以對照這一頁的編號。謝謝大家。