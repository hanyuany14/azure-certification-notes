# Microsoft Foundry Mistakes｜Foundry 錯題

完整概念見 [Microsoft Foundry](../knowledge/04_Microsoft_Foundry.md)。

## Q01 — Knowledge Base 匯入格式的混合年代題

![Original question](../assets/mistakes/Q01_source_image_6.png)

| 快速判斷 | 內容 |
|---|---|
| **舊題庫答案** | **B. DOC、D. PDF** |
| **現行判斷** | 只說 Foundry knowledge base，資訊不足；必須先知道是哪種 knowledge 機制 |
| **線索** | `generate a knowledge base`、`DOC`、`PDF` |
| **考點** | 舊式 Q&A 文件匯入格式；current vs legacy |
| **錯誤原因** | 題幹與解說混用了 Foundry 和舊 Question Answering 語境 |

**為什麼選 B、D：**截圖的解說沿用舊式 Question Answering knowledge base 教材，因此題庫預期 DOC 和 PDF。

| Option | 為什麼是／不是（依舊題） |
|---|---|
| A. CSV | 不是該舊題指定的文件格式；其他現行機制可能處理結構化資料。 |
| **B. DOC** | **是。**符合舊式文件匯入語境。 |
| C. MP4 | 影音內容應看 Content Understanding 等相應能力。 |
| **D. PDF** | **是。**符合舊式文件匯入語境。 |
| E. ZIP | 壓縮檔本身不是該 knowledge base 的內容格式。 |

**補充與延伸：**現行 Foundry 可能使用 File Search、Foundry IQ knowledge source 或 Azure AI Search；支援格式各自不同，不能把 DOC＋PDF 套用到所有機制。

> **記法：先辨認 knowledge 機制，再判斷檔案格式。**

延伸：[Agent tools](../knowledge/04_Microsoft_Foundry.md#6-補充常見-agent-tools)｜來源：[File Search](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/file-search)、[Question Answering format guidelines](https://learn.microsoft.com/en-us/azure/ai-services/language-service/question-answering/reference/document-format-guidelines)

---

## Q09 — 呼叫既有 Prompt Agent

![Original question](../assets/mistakes/Q09_source_image_14.png)

| 快速判斷 | 內容 |
|---|---|
| **答案** | **D. Call `responses.create()` with an `agent_reference`** |
| **線索** | `existing prompt agent`、`send a user message` |
| **考點** | 呼叫既有 agent vs 模型部署 vs ephemeral agent |
| **錯誤原因** | 把 agent name 當成 model deployment name |

**為什麼選 D：**這個題目版本以 `agent_reference` 指向 project 中已存在的 agent，才能重用其 instructions 和 tools。

| Option | 為什麼是／不是 |
|---|---|
| A. Create an ephemeral agent | 建立另一個臨時定義，沒有呼叫既有 agent。 |
| B. `responses.create(model="faq-agent")` | `model` 要放模型部署，不是 agent name。 |
| C. Generate embeddings | 只產生向量，不會執行 agent。 |
| **D. `responses.create()`＋`agent_reference`** | **是。**依題目 API 形狀引用既有 agent。 |

**補充與延伸：**現行 SDK 也可用 `get_openai_client(agent_name=...)` 取得預先綁定 agent 的 client。Agent API 變動快，作答時依題目版本判斷。

> **記法：既有 agent 用 agent 識別方式；`model=` 放 model deployment。**

延伸：[Call an existing agent](../knowledge/04_Microsoft_Foundry.md#call-an-existing-agent)｜來源：[Foundry SDK quickstart](https://learn.microsoft.com/en-us/azure/foundry/quickstarts/get-started-code#chat-with-an-agent)

---

## Q12 — Deployment Option 與 Deployment Type

![Original question](../assets/mistakes/Q12_source_image_17.png)

| 快速判斷 | 內容 |
|---|---|
| **題庫答案** | **D. Standard deployment in Foundry resources** |
| **線索** | `Azure OpenAI model`、`chat app`、`guardrails`、`deployment option` |
| **考點** | Deployment option vs deployment type |
| **錯誤原因** | 把 Provisioned throughput type 當成題目所問的上層 option |

**為什麼選 D：**依這份舊題的用語，Standard deployment 是 Azure OpenAI 模型在 Foundry resource 中的受管理部署選項。

| Option | 為什麼是／不是 |
|---|---|
| A. Instant access | 不建立題目要求的 deployment。 |
| B. Provisioned deployment type | 是預留 throughput／PTU 的 type，不是這題的 option 層級。 |
| C. Managed compute | 用於需要專用 GPU 的 open／custom-weight models，不符合此題。 |
| **D. Standard deployment in Foundry resources** | **是。**符合這份題庫的部署語境。 |

**補充與延伸：**現行文件先分 **Serverless API vs Managed compute**，再於 Serverless 下分 Standard、Provisioned、Batch 等 types。Provisioned 也能搭配治理控制，不能用 guardrails 直接排除它。

> **記法：先看題目問 option，還是 Standard／Provisioned 這類 type。**

延伸：[Deployment option vs type](../knowledge/04_Microsoft_Foundry.md#deployment-option-vs-deployment-type)｜來源：[Deployment overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/deployments-overview)、[Deployment types](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/concepts/deployment-types)

---

## Q13 — AIProjectClient 取得 OpenAI Client

![Original question](../assets/mistakes/Q13_source_image_18.png)

| 快速判斷 | 內容 |
|---|---|
| **答案** | **Call `get_openai_client()`** |
| **線索** | `After creating an AIProjectClient`、`before sending prompts` |
| **考點** | Foundry SDK client layering |
| **錯誤原因** | 混淆 project 管理入口與模型推論 client |

**為什麼選它：**`AIProjectClient` 連接 Foundry project；`get_openai_client()` 取得已設定專案 endpoint 與驗證的 OpenAI client，之後才能呼叫 Responses 或 Chat Completions。

截圖沒有展開全部選項，因此只保留可確認的方向：

| Choice / direction | 為什麼是／不是 |
|---|---|
| **`get_openai_client()`** | **是。**取得模型呼叫 client。 |
| 只有 deployment name | 只有識別值，沒有 client 或 authentication。 |
| Deployments management operations | 管 deployment metadata，不負責送 prompt。 |
| 用 deployment URL 建立 `AIProjectClient` | `AIProjectClient` 使用 project endpoint。 |

**補充與延伸：**一般順序為 `AIProjectClient → get_openai_client() → responses.create()`。SDK 版本可能改變個別介面，但兩層 client 的責任要分清楚。

> **記法：Project client 管專案；OpenAI client 呼叫模型。**

延伸：[Clients](../knowledge/04_Microsoft_Foundry.md#clients看到名稱先判斷責任)｜來源：[AIProjectClient reference](https://learn.microsoft.com/en-us/python/api/azure-ai-projects/azure.ai.projects.aiprojectclient?view=azure-python)

---

## Q19 — Unified Model Invocation API

![Original question](../assets/mistakes/Q19_source_image_26.png)

| 快速判斷 | 內容 |
|---|---|
| **答案** | **B. `responses.create()`** |
| **線索** | `unified model invocation API`、`single prompt` |
| **考點** | Responses API vs Chat Completions API |
| **錯誤原因** | 把模型管理方法或 Chat Completions 當成題目指定的統一介面 |

**為什麼選 B：**`unified model invocation API` 直接指向 Responses API；使用 `responses.create()` 並以 `input=` 傳入內容。

| Option | 為什麼是／不是 |
|---|---|
| A. `deployments.get()` | 讀 deployment 資訊，不執行推論。 |
| **B. `responses.create()`** | **是。**送出 input 並取得模型 response。 |
| C. `models.list()` | 列出模型，不產生回答。 |
| D. `chat.completions.create()` | 仍可使用，但它是 `messages`／`choices` 的另一套 API。 |

**補充與延伸：**Responses 使用 `input`、可加 `instructions`，文字便利欄位是 `output_text`；Chat Completions 使用 `messages` 和 `choices[0].message`。

> **記法：Unified invocation → Responses → `responses.create()`。**

延伸：[Prompt/API shapes](../knowledge/04_Microsoft_Foundry.md#promptapi-shapes避免把兩套欄位混寫)｜來源：[Responses API](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/responses)
