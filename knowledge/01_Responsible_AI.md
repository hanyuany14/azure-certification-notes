# Responsible AI｜負責任的人工智慧


## 2. Core Concepts

### 六大原則

| Official English Term | 中文解釋 | What it does | Input | Output | Exam Keywords |
|---|---|---|---|---|---|
| **Fairness** | 公平性：避免對背景不同、但條件相近的人產生不合理差別待遇 | 檢查資料、預測及資源分配是否形成偏差 | 各群體的資料、決策與錯誤率 | 公平性評估與改善措施 | bias, equitable treatment, demographic groups |
| **Reliability and Safety** | 可靠性與安全性：正常與異常條件下都要穩定運作，失敗時降低傷害 | 測試失敗情境、限制用途、設計安全退場 | 正常案例、例外輸入、邊界情境 | 穩定行為、錯誤處理、風險緩解 | consistent, robust, unexpected conditions, harm |
| **Privacy and Security** | 隱私權與安全性：尊重資料使用目的，防止未授權存取與洩漏 | 保護個人與企業資料，限制收集與存取 | 個資、機密資料、身分與權限 | 受控存取與資料保護措施 | personal data, consent, unauthorized access, encryption |
| **Inclusiveness** | 包容性：讓不同能力、語言與背景的人都能使用並受益 | 改善介面、互動方式與可及性 | 不同使用者的需求、能力與使用環境 | 更廣泛可用的產品體驗 | accessibility, disabilities, empower everyone |
| **Transparency** | 透明度：讓人了解 AI 的用途、能力、限制及相關決策依據 | 說明何時使用 AI、它能做什麼、結果應如何解讀 | 系統行為、模型輸出、使用限制 | 說明、揭露、可理解的理由 | explain, understand, limitations, interpretability |
| **Accountability** | 問責性／責任歸屬：由人類與組織對 AI 的設計、部署及後果負責 | 指定負責人，建立治理、監督及申訴程序 | 角色分工、決策紀錄、部署變更 | 明確責任、稽核軌跡、人類控制 | governance, oversight, ownership, accountable |

此處的 Input／Output 是原則落地時的觀察對象與成果，**不是六個可呼叫 API 的請求／回應格式**。定義依 [Microsoft AI principles](https://www.microsoft.com/en-us/ai/principles-and-approach) 與 [Responsible AI 說明](https://learn.microsoft.com/en-us/azure/machine-learning/concept-responsible-ai?view=azureml-api-2) 整理。

### 具體情境

| 情境 | 最直接的原則 | 判斷理由 |
|---|---|---|
| 招募模型對資格相近、性別不同的申請人有明顯不同通過率 | **Fairness** | 核心在群體之間的待遇與結果差異 |
| 自動駕駛遇到感測器故障時進入安全模式 | **Reliability and Safety** | 核心在異常條件下的安全行為 |
| 客服系統不得把客戶個資顯示給其他客戶 | **Privacy and Security** | 核心在資料邊界與未授權揭露 |
| 加入語音、字幕與螢幕閱讀器支援，讓視障、聽障者可使用 | **Inclusiveness** | 核心在不同能力的人能否參與 |
| 向客戶說明貸款評估用了哪些因素，並揭露模型限制 | **Transparency** | 核心在理解系統與決策 |
| 指定人員負責審查模型變更，建立申訴與稽核流程 | **Accountability** | 核心在誰負責、誰監督，以及如何追溯；「誰」指人與組織 |


### Responsible Generative AI 與 NIST AI RMF

**NIST (National Institute of Standards and Technology)** 是美國的標準與技術機構。**NIST AI Risk Management Framework (AI RMF)** 是供自願採用的風險管理框架，目標是將 trustworthy AI 的考量納入 AI 系統設計、開發、使用與評估。它不是 Azure 產品，也不是 AI-901 Study Guide 明列的產品操作目標。

| Framework / flow | 官方英文階段 | 中文理解 | 考試判讀 |
|---|---|---|---|
| **NIST AI RMF 1.0** | **GOVERN → MAP → MEASURE → MANAGE** | 治理、理解情境與風險、衡量、管理 | 了解外部風險框架；NIST 現在正修訂 1.0，不能把它當成 Foundry API |
| **Microsoft Responsible Generative AI practices** | **Identify → Measure → Mitigate → Operate** | 找出傷害、衡量、降低傷害、負責任營運 | Microsoft 文件中，第一階段是 **Identify potential harms** |

兩組名稱不是逐字相同。Microsoft 說其四階段與 NIST AI RMF functions **closely correspond**；可將 NIST 的治理理解為貫穿 Microsoft 的四階段，而不是硬把每個名稱做一對一翻譯。

## 3. How It Works

Responsible AI 應貫穿生命週期。以下是依六大原則整理的讀題與設計流程：

| 步驟 | 要確認的事 | 可觀察的成果 |
|---|---|---|
| 1. Define purpose | 系統幫誰完成什麼工作？不適用哪些情境？ | 用途、受影響者與限制說明 |
| 2. Assess risks | 哪些群體可能被排除？錯誤會造成什麼傷害？會碰到哪些資料？ | 公平、可及性、安全與隱私風險清單 |
| 3. Design controls | 要如何限制存取、處理失敗、提供人工覆核？ | 權限、防護、回退與介面設計 |
| 4. Evaluate | 不只看平均品質，也看特定群體及異常案例 | 測試結果、已知限制與修正紀錄 |
| 5. Monitor and govern | 上線後誰追蹤失敗、核准變更、處理申訴？ | 監控、責任人、持續改善 |

例如聊天 Agent 可能同時需要「以字幕支援聽障者」「避免洩漏聊天紀錄」「由主管覆核高影響動作」。三者分別對應 Inclusiveness、Privacy and Security、Accountability，並不互斥。[Agent responsible AI 指引](https://learn.microsoft.com/en-us/agents/center-of-excellence/responsible-ai) 也強調設計階段就應考慮原則。

對生成式 AI，Microsoft 的實務流程如下：

```text
Identify potential harms
  → Measure frequency and severity
  → Mitigate with layered controls
  → Operate with readiness, monitoring, feedback, and incident response
```

例如客服聊天系統先列出錯誤資訊、偏見、個資洩漏與過度依賴等風險，再建立測試集衡量頻率與嚴重性；之後透過 guardrails、system prompt、UX 提醒及人工流程降低風險，最後才以監控、回饋與 rollback 計畫營運。

## 5. Comparison / Common Confusions

| Concept A | Concept B | Key Difference |
|---|---|---|
| **Fairness** | **Inclusiveness** | Fairness 看待遇或結果是否不合理地偏向某群體；Inclusiveness 看誰能使用、誰被排除 |
| **Reliability and Safety** | **Privacy and Security** | 系統故障或不安全決策偏向前者；個資外洩或未授權存取偏向後者 |
| **Transparency** | **Accountability** | 解釋「如何運作與有哪些限制」偏向前者；規定「誰負責與誰監督」偏向後者 |
| **Accuracy** | **Fairness** | 整體預測準確率高，不代表每個群體受到合理待遇 |
| **Explainability** | **Correctness** | 能提供可理解說明，不代表答案一定正確 |
| **Responsible AI** | **Shared Responsibility Model** | 前者是 AI 設計／使用原則；後者劃分雲端供應商與客戶的管理責任 |
| **Safety filter** | **Governance** | 過濾器是技術控制；治理還包括負責人、政策、審核與追蹤 |

讀題先找「題目最想改善的問題」，再選最直接的原則。不能只看到安全一詞就忽略它是在講身體傷害、系統可靠性，還是資料保護。


## 7. Exam Keywords & Triggers

| If the question says... | Think... |
|---|---|
| biased outcomes / similar qualifications / demographic groups | **Fairness** |
| unexpected conditions / safe failure / reliable operation | **Reliability and Safety** |
| personal information / consent / unauthorized disclosure | **Privacy and Security** |
| disability / accessibility / different abilities | **Inclusiveness** |
| explain decisions / disclose limitations / understand behavior | **Transparency** |
| governance / oversight / named owner / responsibility | **Accountability** |

## 8. Memory Rules

- **Fairness**：待遇公平；**Inclusiveness**：人人能參與。
- **Reliability and Safety**：穩定且不傷害；**Privacy and Security**：資料與存取要保護。
- **Transparency**：說得清楚；**Accountability**：有人負責。
- **High accuracy ≠ fair outcomes**；單一分數不能代表整體負責任程度。
- GenAI 風險流程：**找 → 量 → 降 → 管**（Identify → Measure → Mitigate → Operate）。

## 9. Official Sources

以下為本模組使用的英文官方來源，查核日期 **2026-09-06**：

| 官方文件 | 支持內容 |
|---|---|
| [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) | 六大原則的考試範圍 |
| [Microsoft Responsible AI principles and approach](https://www.microsoft.com/en-us/ai/principles-and-approach) | 六項原則的正式名稱與意義 |
| [What is Responsible AI?](https://learn.microsoft.com/en-us/azure/machine-learning/concept-responsible-ai?view=azureml-api-2) | 公平性、安全、透明度、隱私與人類治理 |
| [Apply responsible AI](https://learn.microsoft.com/en-us/agents/center-of-excellence/responsible-ai) | Agent 設計與治理中的原則運用 |
| [Azure AI Content Safety overview](https://learn.microsoft.com/en-us/azure/ai-services/content-safety/overview) | 內容分析、Prompt Shields、Preview 能力 |
| [Azure RBAC overview](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview) | 資源授權的用途 |
| [Responsible AI practices for Azure OpenAI models](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/overview) | Identify、Measure、Mitigate、Operate 與緩解層次 |
| [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) | NIST AI RMF 的自願採用、風險管理目的與 1.0 修訂狀態 |

原始材料：`source/901 files 2/AI-901 Notes 3cf7254587ee802da82de782e6a42941.md` 的 Responsible AI 表格與相關題目解釋；`image 1.png` 投影片。原始題目截圖仍完整保存在 `source/`，本階段只整理其中的知識。
