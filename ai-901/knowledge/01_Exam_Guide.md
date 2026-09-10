# AI-901 考前須知｜流程、方向與練習方式

這份筆記用於考前快速閱讀。

AI-901 英文考綱自 **2026-04-15** 起分為兩大部分：

| Domain | Weight | 準備重點 |
|---|---:|---|
| **Identify AI concepts and capabilities** | 40–45% | Responsible AI、模型概念、text、speech、vision、information extraction |
| **Implement AI solutions by using Microsoft Foundry** | 55–60% | Prompt、模型部署、Portal、agent、Foundry SDK、各 Foundry Tools |

官方提醒考生要熟悉 Python 基本語法，以及 REST APIs、SDKs 和 CLIs。多數題目以 GA 功能為主，也可能包含常用 Preview 功能。

建議可以先閱讀一下官方指南：https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901

## 2. 考試時間與題型

| Item | Official / experience |
|---|---|
| **Exam duration** | Fundamentals 正式答題時間為 **45 分鐘**；seat time 為 65 分鐘，另含說明與意見回饋 |
| **Question count** | Microsoft 認證考試通常約 40–60 題，但會變動 |
| **本次場次** | **Personal experience：42 題／45 分鐘**，右上角有倒數計時 |
| **常見題型** | 單選、複選、下拉填空、drag-and-drop、程式碼欄位／method 選擇 |
| **Microsoft Learn** | Fundamentals 考試期間不能開啟 Microsoft Learn |

> 啾啾筆記：通常 code 題目是讀懂 client、method、parameter 與 input/output，不是從零寫出完整應用程式。例如文字加圖片的 multimodal input，先辨認 `input_text` 與 `input_image`；不需要硬背程式碼喔～

### 答題策略

1. **選擇最直接的答案：**有些選項看起來都能達成需求，但單選題要選最直接、最符合題目主要目標的方法，不必加入題目沒有要求的額外功能。
2. **從情境中找關鍵字：**考試常以「工程師或公司要建立某種 app」描述需求。先找出題目的 **input、output、動作與限制**，再對應 Azure AI 服務。例如 `transcribe` 對應 Speech to Text，`sentiment` 對應 Azure Language，`structured fields` 則優先想到 Content Understanding。

> **快速記法：**先判斷「輸入什麼、要產出什麼」，再選最直接完成需求的服務。

## 3. 出題方向｜Personal experience

以下是這次場次的體感，用來調整複習優先順序，不代表每次都會出現相同題量。

| Priority | 觀察與準備方式 |
|---:|---|
| ⭐⭐⭐⭐⭐ | **Responsible AI** 題目不少；六大原則與情境關鍵字應盡量拿分 |
| ⭐⭐⭐⭐⭐ | **Service boundary** 很重要；先看需求的 input、output，再選服務 |
| ⭐⭐⭐⭐⭐ | **Foundry Portal**：實際點過 Discover、Build、Operate、Agents、Evaluations |
| ⭐⭐⭐⭐ | **Foundry SDK / clients**：知道每個 client 負責什麼，無需背完整 syntax |
| ⭐⭐⭐⭐ | **程式辨識**：本次約有 10–15 題涉及程式、SDK、API 或參數，明顯多於練習題體感 |
| ⭐⭐⭐⭐ | **Speech keywords**：transcribe、translate、recognition、synthesis 與 input/output |
| ⭐⭐ | Tenant、Subscription、Resource Group 等 Azure 管理層級不是本次重點；connection、endpoint、credential 仍需懂 |

### Service boundary 快速表

| 題目需求 | 優先想到 |
|---|---|
| 分析文字的 sentiment、entities、key phrases | [Azure Language](05_Text_and_Language.md) |
| Speech-to-text、text-to-speech、speech translation | [Azure Speech](06_Speech.md) |
| 看懂圖片或回答圖片問題 | [Vision-capable model / Azure Vision](07_Computer_Vision.md) |
| 從 document、image、audio、video 擷取結構化資料 | [Content Understanding](08_Content_Understanding.md) |
| 選模型、部署、建立／測試 agent | [Microsoft Foundry](04_Microsoft_Foundry.md) |
| 搜尋企業文件與 index | Azure AI Search；基礎詞彙見 [Foundry](04_Microsoft_Foundry.md#search-vocabulary把資料變成可檢索內容) |
| 依 prompt 建立新圖片 | Image-generation model |


## 4. Foundry Portal 考前操作

至少實際點過一次以下區域，建立畫面與功能的連結：

```text
Discover → Model catalog / Model leaderboard → 找模型、比較模型
Build    → Models / Agents / Evaluations      → 建立與改善方案
Operate  → Tracing / Compliance / fleet       → 觀察與治理營運狀態
Manage   → Quota / Project details / Connections
```

- **Model catalog**：探索模型與 model card。
- **Model leaderboard**：依 quality、safety、cost、throughput 等 benchmark 比較模型；目前為 **Preview**。
- **Evaluation**：對模型、app 或 agent 執行評估。
- **Evaluator**：評分規則，例如 relevance、groundedness、coherence、fluency 或 agent tool-use metrics。
- **Agent name**：既有 agent 的識別值，不是 model deployment name。
- **Connection**：Project 連到 Azure OpenAI、Storage、AI Search 等外部資源的設定。

## 5. Speech 關鍵字

| Keyword | Input → Output |
|---|---|
| **Transcribe / Recognition / Speech to Text** | Speech → Text |
| **Text to Speech / Speech Synthesis** | Text → Speech |
| **Speech Translation** | Speech → 另一語言的 text / speech |
| **Diarization** | 多人語音 → transcript＋speaker labels |
| **Dictation** | 單人口述 → 要寫下來的文字 |

客服通話通常是跨服務流程：Speech 處理 transcription／diarization，Language 可做 PII、sentiment 與 summarization；若題目指定多模態 schema 與 analyzer，考慮 Content Understanding 的 `prebuilt-callCenter`。

## 6. Practice Assessment 練習方式

Practice Assessment 可免費重做，並提供答案理由與學習連結；它反映題目風格，但不等於正式題目、題數或完整難度。

```text
做 Practice Assessment
→ 不只記答案，也解釋其他選項為什麼錯
→ 把不熟概念補進 knowledge / mistakes
→ 隔一段時間重做
→ 考前只讀自己容易混淆的表格與錯題
```

建議另外使用官方 **Exam Sandbox** 熟悉操作方式。練習程式時先遮住 method 或 parameter，再根據變數名稱與資料流填回去。

## 7. OnVUE 在家考流程

### 考前

- 使用正式考試當天的電腦、網路與地點完成 system test。
- 確認 webcam、microphone、網路、電源與系統管理員權限。
- 證件姓名必須與 Microsoft Certification profile 相符。
- 清空桌面；移除紙張、筆、書籍、手錶與未核准裝置；額外螢幕需拔除並轉開。
- 房間需封閉且無其他人進入。

### Check-in 與考試中

- 可在預約時間前 **30 分鐘**開始 check-in，最晚到預約後 15 分鐘。
- 依流程拍攝本人、證件與考試區域照片，再等待 greeter／proctor 核對。
- Proctor 會透過 webcam 與 microphone 遠端監考，通常只在有問題時聯絡。
- 手機完成 check-in 後放到手臂拿不到、但能聽見來電的位置。
- 考試畫面有倒數計時，不需自備手錶。

> **Personal experience：**本次房間拍攝與核對流程順利，監考員沒有在考試途中打斷；完成 check-in 後也可能提早開始。這是單次體驗，實際仍以 proctor 指示與當日規則為準。

## 8. 推薦觀看順序

1. [AI考照全解析：拆解 AI-901 的奪分地圖](https://youtu.be/M8nIlmDizdc?si=3N-ML5FIds-I8huv)
2. [別再死背 AI-900！全新 AI-901 考綱、Azure Foundry 與 Python 實作](https://youtu.be/Wmxb4l_xVAE?si=nw10YrYGONQ0Q-5N)

**Personal recommendation：**兩支影片的整理方向與實際考試體感接近，適合考前建立全貌；仍應用現行 English study guide 和 Microsoft Learn 核對產品名稱與生命週期。

## 9. 最後複習順序

1. 看完兩支推薦影片。
2. 熟記 Responsible AI 六大原則與關鍵字。
3. 讀 Foundry Portal、client、evaluation 與 identifiers。
4. 用 service boundary 表快速區分 Language、Speech、Vision、Content Understanding。
5. 複習常見 SDK input/output 與 method 名稱。
6. 完成 Practice Assessment，最後只回看錯題。

## 10. Official Sources

核對日期：**2026-09-10**。

- [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)
- [Exam duration and exam experience](https://learn.microsoft.com/en-us/credentials/support/exam-duration-exam-experience)
- [About online exams with Pearson VUE](https://learn.microsoft.com/en-us/credentials/certifications/online-exams)
- [Practice Assessments for Microsoft Certifications](https://learn.microsoft.com/en-us/credentials/certifications/practice-assessments-for-microsoft-certifications)
- [Model leaderboards in Microsoft Foundry](https://learn.microsoft.com/en-us/azure/foundry/concepts/model-benchmarks)
- [Current Foundry portal navigation](https://learn.microsoft.com/en-us/azure/foundry/how-to/navigate-from-classic)
