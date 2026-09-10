# AI-901 考前須知｜流程、方向與練習方式

這一篇是我考完 AI-901 後整理的心得，主要記錄我覺得值得準備的方向、答題方式和線上考試流程。考前快速看過一次就可以了。

## 1. 先認識考試範圍

AI-901 英文考綱自 **2026-04-15** 起分成兩大部分：

| Domain | Weight | 準備重點 |
|---|---:|---|
| **Identify AI concepts and capabilities** | 40–45% | Responsible AI、模型概念、text、speech、vision、information extraction |
| **Implement AI solutions by using Microsoft Foundry** | 55–60% | Prompt、模型部署、Portal、agent、Foundry SDK、各 Foundry Tools |

官方也有提醒要熟悉 Python 基本語法，以及 REST APIs、SDKs 和 CLIs。大部分會以 GA 功能為主，但也可能出現常用的 Preview 功能。

> 啾啾筆記：我會建議準備前先看一次 [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)，先知道官方現在要考什麼，再開始整理筆記，才不會花太多時間讀到已經不重要的舊內容。

## 2. 考試時間與題型

| Item | 我整理的重點 |
|---|---|
| **Exam duration** | Fundamentals 正式答題時間是 **45 分鐘**；seat time 是 65 分鐘，另外包含說明與意見回饋 |
| **Question count** | Microsoft 認證考試通常約 40–60 題，但每次可能不同 |
| **我這次的場次** | **42 題／45 分鐘**，畫面右上角會顯示倒數時間 |
| **常見題型** | 單選、複選、下拉填空、drag-and-drop，以及程式碼欄位／method 選擇 |
| **Microsoft Learn** | Fundamentals 考試期間不能開啟 Microsoft Learn |

> 啾啾筆記：通常 code 題目是讀懂 client、method、parameter 與 input/output，不是從零寫出完整應用程式。例如文字加圖片的 multimodal input，先辨認 `input_text` 與 `input_image`；不需要硬背程式碼喔～

### 我的答題策略

1. **選最直接的答案：**有些選項看起來都能達成需求，但單選題只要選最直接、最符合主要目標的方法就好，不用自己加上題目沒有要求的功能。
2. **從情境中找關鍵字：**題目很常說某個工程師或公司想建立一種 app，再問你需要哪個 Azure AI 服務。這時先找 **input、output、動作和限制**，通常就能判斷答案。

例如：

- 看到 `transcribe` → Speech to Text
- 看到 `sentiment` → Azure Language
- 看到 `structured fields` → Content Understanding
- 看到建立或呼叫 agent → Microsoft Foundry

> 啾啾筆記：先關注說「輸入是什麼？最後要得到什麼？」再選最直接完成需求的服務，通常就不容易被其他選項干擾。

## 3. 我會先看這兩支影片

1. [AI 考照全解析：拆解 AI-901 的奪分地圖](https://youtu.be/M8nIlmDizdc?si=3N-ML5FIds-I8huv)
2. [別再死背 AI-900！全新 AI-901 考綱、Azure Foundry 與 Python 實作](https://youtu.be/Wmxb4l_xVAE?si=nw10YrYGONQ0Q-5N)

我覺得這兩支影片整理的方向和實際考試體感很接近，很適合先建立整體概念。不過產品名稱和功能狀態仍可能更新，最後還是要用英文 Study Guide 和 Microsoft Learn 核對。

## 4. 我覺得重要的出題方向

以下是我這次考試的感受，不代表每一場都會有相同的題數，但可以拿來安排複習順序。

| Priority | 我的準備建議 |
|---:|---|
| ⭐⭐⭐⭐⭐ | **Responsible AI** 題目不少；六大原則和情境關鍵字要熟，這些題目應該盡量拿分 |
| ⭐⭐⭐⭐⭐ | **Service boundary** 很重要；先看 input 和 output，再判斷應該選哪一個服務 |
| ⭐⭐⭐⭐⭐ | **Foundry Portal** 最好自己打開操作過，知道 Discover、Build、Operate 和 Manage 大約放了什麼 |
| ⭐⭐⭐⭐ | **Foundry SDK / clients** 要知道每個 client 的用途，但不用硬背完整 syntax |
| ⭐⭐⭐⭐ | **程式題**：我這次大約有 10–15 題涉及程式、SDK、API 或參數，比 Practice Assessment 的體感更多 |
| ⭐⭐⭐⭐ | **Speech keywords**：transcribe、translate、recognition、synthesis，以及它們的 input/output |
| ⭐⭐ | Tenant、Subscription、Resource Group 不是我這次的重點，但 connection、endpoint 和 credential 還是要懂 |

### Service boundary 快速表

| 題目需求 | 優先想到 |
|---|---|
| 分析文字的 sentiment、entities、key phrases | [Azure Language](05_Text_and_Language.md) |
| Speech-to-text、text-to-speech、speech translation | [Azure Speech](06_Speech.md) |
| 看懂圖片或回答圖片問題 | [Vision-capable model / Azure Vision](07_Computer_Vision.md) |
| 從 document、image、audio、video 擷取結構化資料 | [Content Understanding](08_Content_Understanding.md) |
| 選模型、部署、建立或測試 agent | [Microsoft Foundry](04_Microsoft_Foundry.md) |
| 搜尋企業文件與 index | Azure AI Search；基礎詞彙見 [AI Models and Workloads](03_AI_Models_and_Workloads.md#search-vocabulary把資料變成可檢索內容) |
| 依 prompt 建立新圖片 | Image-generation model |

## 5. Foundry Portal 考前可以摸一次

我會建議把 Foundry Portal 的主要區域和功能都點過一次，知道每一個功能大概放在哪裡就可以了。

```text
Discover → Model catalog / Model leaderboard → 找模型、比較模型
Build    → Models / Agents / Evaluations      → 建立與改善方案
Operate  → Tracing / Compliance / fleet       → 觀察與治理營運狀態
Manage   → Quota / Project details / Connections
```

- 在 **Build** 裡實際建立一次 Agent，看看 instructions、model 和 Tools 怎麼設定。
- 看一下如何透過 **REST API、SDK 或程式碼範例**呼叫已經建立的 Agent。
- 每個頁面和功能都點過一次即可；遇到看不懂的功能，就按旁邊的 **`?` icon** 看提示，或直接詢問 Portal 裡的 AI。

> 啾啾筆記：不用把整個 Portal 背起來，只要真的操作過一次，知道功能名稱、位置和大概流程，考試看到畫面或情境時就比較容易判斷。

## 6. Practice Assessment 怎麼練

我覺得 Practice Assessment 很值得多做幾次。題目可以免費重做，而且官方會提供答案理由和學習資源。它不等於正式考試的完整難度，但很多概念只是換個方式再問一次。

- [AI-901 Practice Assessment](https://aiskillsnavigator.microsoft.com/credentials/cert-83587e0a0754cfee561ade3e27d9fa1cdaf15ae03be52d2413b2b858d1b4eda4)
- [其他 Microsoft Certification Practice Assessments](https://learn.microsoft.com/en-us/credentials/certifications/practice-assessments-for-microsoft-certifications)

```text
做 Practice Assessment
→ 不只記答案，也看官方為什麼選這個答案
→ 把每個錯誤選項不適合的原因弄懂
→ 將不熟的概念補進 knowledge / mistakes
→ 隔一段時間再重做
→ 考前只看自己容易混淆的筆記和錯題
```

> 啾啾筆記：做到後面可能會開始遇到重複題，這時不要只靠記憶選答案。試著重新說一次「為什麼選它、其他選項為什麼不適合」，這樣正式考試換個問法也比較不會怕。

## 7. OnVUE 在家考流程

### 考前準備

- 用正式考試當天的電腦、網路和地點完成 system test。
- 確認 webcam、microphone、網路、電源和系統管理員權限。
- 證件姓名要和 Microsoft Certification profile 相同。
- 清空桌面，移除紙張、筆、書籍、手錶和未核准裝置；額外螢幕也要拔除並轉開。
- 確保房間可以關閉，而且考試途中不會有其他人進入。

### Check-in 與考試中

- 預約時間前 **30 分鐘**就可以開始 check-in，最晚不要超過預約時間後 15 分鐘。
- 跟著流程拍本人、證件和考試空間，再等待 greeter／proctor 核對。
- Proctor 會透過 webcam 和 microphone 遠端監考，通常有問題時才會聯絡你。
- 手機完成 check-in 後，要放在手拿不到、但還聽得到來電的位置。
- 考試畫面會倒數，不用另外準備手錶。

> 啾啾筆記：我這次的房間拍攝和核對都很順利，監考員也沒有在考試途中打斷我。完成 check-in 後可能可以提早開始，所以不用太緊張。不過這只是我的單次經驗，當天還是以 proctor 的指示為準喔～

## 8. Official Sources

核對日期：**2026-09-10**。

- [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)
- [Exam duration and exam experience](https://learn.microsoft.com/en-us/credentials/support/exam-duration-exam-experience)
- [About online exams with Pearson VUE](https://learn.microsoft.com/en-us/credentials/certifications/online-exams)
- [Practice Assessments for Microsoft Certifications](https://learn.microsoft.com/en-us/credentials/certifications/practice-assessments-for-microsoft-certifications)
- [Model leaderboards in Microsoft Foundry](https://learn.microsoft.com/en-us/azure/foundry/concepts/model-benchmarks)
- [Current Foundry portal navigation](https://learn.microsoft.com/en-us/azure/foundry/how-to/navigate-from-classic)
