# Microsoft Foundry｜模型、應用程式與代理程式

驗證日期：**2026-09-06**。本模組把原筆記的 Foundry、Search、Agent tools、SDK、deployment 整合成同一條學習路徑：**選模型 → 部署／測試 → 寫 prompt → 接 SDK → 加工具與知識 → 評估**。

## 1. Module Overview

| 欄位 | 重點 |
| --- | --- |
| Official service/module name | **Microsoft Foundry**；Foundry Models；Foundry Agent Service |
| AI-901 relevance (High/Medium/Low) | **High**：模型部署、system/user prompts、Foundry SDK、single-agent 與 client；**Medium**：Search／Foundry IQ 細節作 grounding 背景 |
| Current status (Current/Legacy/Mixed) | **Mixed**：目前 Foundry 核心＋明確標示的 Preview 功能與舊版名稱／API 對照 |
| Main exam workloads | Generative AI apps、single-agent solutions、tool use、retrieval-grounded answers |
| Primary Microsoft products | Microsoft Foundry、Foundry Models、Foundry Agent Service、Azure AI Search、Microsoft Entra ID |

目前考綱要求在 portal 部署與互動、建立有效提示、使用 SDK 建立輕量聊天 client，以及建立／測試單一 agent。來源：[AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)。模型基本原理、取樣參數與 metrics 見 [AI Models and Workloads](02_AI_Models_and_Workloads.md)。

## 2. Core Concepts

### Foundry platform map

![原始 Microsoft Foundry 總覽投影片：Models、Agents、Tools、Knowledge](../assets/knowledge/03_foundry_overview.png)

**更正／補充（2026-09-05）：**「中央廚房」是記憶比喻；Foundry 提供整合開發能力，不表示每個服務都不需要另外配置資源或權限。投影片的模型數量、供應商／版本不是永久清單，實際可用性要查 model card。**Foundry IQ** 的部分能力 GA、部分 **Preview**，取決於 Search API；portal 的 agentic retrieval 仍有 Preview 存取範圍。**Indexed knowledge sources** 才會擷取、分塊與建立索引，**remote sources** 是查詢時取資料，不能一概說所有來源都自動索引。來源：[Foundry capability map](https://learn.microsoft.com/en-us/azure/foundry/concepts/capabilities)、[What is Foundry IQ](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-foundry-iq)。

| Official term | 繁體中文解釋 | Input → Output / example | Exam distinction |
| --- | --- | --- | --- |
| **Foundry Models / Model catalog** | 發現、比較與選擇模型的入口 | 任務需求 → 適合的 model card／部署選項 | Catalog 是選模型；不等於模型已部署 |
| **Foundry resource / Project** | 資源提供服務能力；project 組織開發使用的部署、agent、連線等 | 一個客服專案 → 管理相關元件 | Project endpoint 與模型部署名稱是不同值 |
| **Model deployment** | 把所選模型提供給推論請求使用 | Model＋deployment settings → callable deployment | 呼叫通常使用自行命名的 deployment name |
| **Foundry Agent Service** | 提供 agent 的建立、執行與管理能力 | Model＋instructions＋tools → agent | Agent 是有設定與工具的應用，不是另一個 model name |
| **Foundry Tools** | 把語言、語音、視覺、內容理解等能力整合進解決方案 | Text／audio／image／documents → 任務結果 | 特定服務 SDK 見對應模組 |
| **Knowledge / Grounding** | 提供模型回答所需的外部資料 | 使用者問題＋文件 → 有資料依據的回覆 | 模型不會因為部署在 Azure 就自動讀到企業資料 |
| **Foundry IQ** | 由 Azure AI Search 支援、可供多 agent 共用的知識層 | Query＋knowledge base → retrieved content／references | 不是所有 agent 的必要元件；也不是重新訓練模型 |

平台分類依據：[Foundry capability map](https://learn.microsoft.com/en-us/azure/foundry/concepts/capabilities)、[Foundry Models overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/foundry-models-overview)。Project 與 client 的關係見 [Azure AI Projects SDK](https://learn.microsoft.com/en-us/python/api/overview/azure/ai-projects-readme?view=azure-python)。

### Prompts：English instructions first

| Prompt component | 作用 | 自建範例 | 判斷重點 |
| --- | --- | --- | --- |
| **System prompt / Instructions** | 設定角色、規則、範圍、風格與工具使用 | `You help employees understand company travel policy.` | 是行為設定，不是當次問題；是否保留由 API／agent 定義決定 |
| **User prompt** | 當次要完成的任務與輸入 | `Summarize the reimbursement rules in three bullets.` | 清楚說明動作、輸入、輸出格式 |
| **Context / Grounding data** | 本次可引用的資料 | 最新差旅規章片段＋來源標識 | 文件內容是參考資料，不自動成為更高優先指示 |
| **Few-shot examples** | 用少量範例示範輸入與輸出模式 | 兩組客服問句 → 指定格式的摘要 | 範例放在 prompt，不等於 fine-tuning |
| **Output format / Constraints** | 明確要求可使用的結果形狀 | `Return a table with Rule, Limit, and Source.` | 需要嚴格結構時，另確認模型/API 的 structured-output 支援 |

System message 指導模型行為，但不是完整安全保證；要和評估、grounding、內容安全及權限一起設計。來源：[Safety system messages](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/system-message)、[Prompt engineering techniques](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/prompt-engineering)。

### Search vocabulary：把資料變成可檢索內容

| Concept | 繁體中文解釋 | Input → Output | Example / keyword |
| --- | --- | --- | --- |
| **Azure AI Search** | 搜尋與檢索服務，支援企業資料 grounding | 資料＋query → ranked results | 找員工手冊相關段落 |
| **Search index** | 以欄位組織的可搜尋文件集合 | JSON documents → searchable fields | `id`、`title`、`content`、`vector` |
| **Indexer** | 從支援的資料來源讀取、映射欄位並填入 index | Source → index | 定期把 Blob 文件更新進索引；**pull** |
| **Full-text search** | 以字詞和文字查詢檢索 | Search text → matches | 產品代碼、姓名、專有名詞 |
| **Vector search** | 比較 embedding 向量的相似性 | Query vector → similar vectors/documents | 「差旅費」與「出差報銷」可語意相近 |
| **Hybrid search** | 同一請求同時做全文與向量搜尋，合併排名 | Text＋vector → merged results | 同時保留字詞精確度與語意相似度 |
| **Semantic ranker** | 對初步結果以語意再排序 | Ranked candidates＋query → reranked candidates | 把真正符合問題的段落往前排 |
| **AI enrichment / Skillset** | 在 indexing pipeline 加入 OCR、分塊、實體擷取或向量化等 | Raw content → enriched fields | 掃描 PDF 先取文字再檢索 |
| **Knowledge mining** | 從大量非結構化資料擷取可用知識的情境 | 文件集合 → 可搜尋的資訊 | 名詞歷史與深度補充見 module 08 |

Index／indexer／enrichment 依據：[Azure AI Search overview](https://learn.microsoft.com/en-us/azure/search/search-what-is-azure-search)、[Indexer overview](https://learn.microsoft.com/en-us/azure/search/search-indexer-overview)。Hybrid 使用 **Reciprocal Rank Fusion (RRF)** 合併結果；semantic ranker 是後續 reranking，不是 vector search 的別名。來源：[Hybrid search](https://learn.microsoft.com/en-us/azure/search/hybrid-search-overview)、[Semantic ranking](https://learn.microsoft.com/en-us/azure/search/semantic-search-overview)。

## 3. How It Works

### Portal workflow：從模型到單一 agent

| Step | 做什麼 | 要看懂的結果 |
| --- | --- | --- |
| **1. Select project and model** | 在目標 project 比較模型能力、輸入輸出、區域與限制 | 選的是可完成任務的模型，不只選知名型號 |
| **2. Deploy and test** | 選支援的 deployment option/type，在 playground 試 prompt | 記下 project endpoint 與 deployment name |
| **3. Improve instructions** | 加上角色、背景、輸出格式，用代表性輸入重測 | 用同一組案例比較修改前後 |
| **4. Create single agent** | 選模型、寫 instructions、配置所需 tools／knowledge | 清楚知道 agent 可以做哪些動作 |
| **5. Test agent behavior** | 測正常問題、缺少資料、模糊要求與工具回傳 | 檢查是否選對工具、引用合適資料 |
| **6. Connect client** | 用相符版本的 SDK 接 project，呼叫模型或 agent | 區分直接 model inference 與 agent invocation |

這是學習操作順序，portal 按鈕位置可能調整。平台與 SDK 依據：[Deployment overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/deployments-overview)、[Azure AI Projects SDK](https://learn.microsoft.com/en-us/python/api/overview/azure/ai-projects-readme?view=azure-python)。

### Retrieval-Augmented Generation (RAG)

```text
Indexing path:
Documents → extraction/chunking → optional embeddings → search index

Question path:
User question → retrieve relevant chunks → add grounded context to prompt
→ model generates answer → app presents answer and available citations
```

RAG 不要求所有系統一定用 vector search；搜尋方式由需求決定。檢索資料仍要符合使用者的權限。更詳盡的 Azure ML／knowledge mining 背景放在 [Legacy and Adjacent Topics](08_Legacy_and_Adjacent_Topics.md)。來源：[Azure AI Search](https://learn.microsoft.com/en-us/azure/search/search-what-is-azure-search)、[RAG prompt construction](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/rag/rag-prompt-engineering)。

### Agent tool loop

| Stage | 發生什麼 | 自建例子 |
| --- | --- | --- |
| **Receive goal** | 將使用者需求交給有設定的 agent | 「查詢這筆訂單目前狀態」 |
| **Select tool** | 模型依 instructions 和 tool descriptions 決定是否需要工具 | 選擇訂單查詢工具，提供 order ID |
| **Execute tool** | 對應的 runtime 或應用程式執行工具 | 後端 API 回傳配送狀態 |
| **Use result** | 把工具結果提供給模型繼續處理 | 依真實狀態回答使用者 |

Function tool 的模型輸出是呼叫要求與 arguments，不表示模型自己已執行任意 Python／API；執行方要處理呼叫並回傳結果。託管工具則由對應服務執行。來源：[AI agents](https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/7-agents)、[Foundry SDK tool examples](https://learn.microsoft.com/en-us/python/api/overview/azure/ai-projects-readme?view=azure-python)。

## 4. Important API / SDK Patterns

### Project endpoint → client → deployed model

本節核對官方 **Azure AI Projects 2.5.0 stable reference**（2026-09-06）。前版筆記引用 `2.0.0b4` 的舊 Preview 視圖，不能據此把整個 2.x SDK 標成 Preview。Stable package 仍可能包含個別 Preview 功能，必須逐功能看狀態；以下基本 client／Responses 用法不需要因舊文件而加 `--pre`。

執行前需安裝相符的 `azure-ai-projects`、`azure-identity`、`openai`，並準備已部署且支援 Responses 的模型、Entra 登入與適當角色。這些環境變數名稱是範例自訂的，重要的是內容正確。來源：[Stable SDK reference](https://learn.microsoft.com/en-us/python/api/overview/azure/ai-projects-readme?view=azure-python)。

| Value / identifier | 意義 | Example shape |
| --- | --- | --- |
| **Project endpoint** | Foundry 專案位址 | `https://<resource>.services.ai.azure.com/api/projects/<project>` |
| **Model deployment name** | 專案可使用的模型部署名稱 | `study-chat`，可與模型型號不同 |
| **Agent name** | 已建立的 agent 名稱 | `faq-agent` |
| **Conversation ID** | 持續對話的狀態識別碼 | 由 Conversations API 回傳 |

```python
import os

from azure.ai.projects import AIProjectClient
from azure.identity import DefaultAzureCredential

# Prerequisites: authenticated identity, project access, deployed Responses model.
with (
    DefaultAzureCredential() as credential,
    AIProjectClient(
        endpoint=os.environ["AZURE_AI_PROJECT_ENDPOINT"],
        credential=credential,
    ) as project_client,
    project_client.get_openai_client() as openai_client,
):
    response = openai_client.responses.create(
        model=os.environ["AZURE_AI_MODEL_DEPLOYMENT_NAME"],
        input="Explain Azure AI Search in one sentence.",
    )
    print(response.output_text)
```

**順序：`AIProjectClient` → `get_openai_client()` → `responses.create()` → `response.output_text`。** `DefaultAzureCredential` 提供 token credential；這段不是把 API key 當成 project endpoint。來源：[Azure AI Projects Python reference](https://learn.microsoft.com/en-us/python/api/overview/azure/ai-projects-readme?view=azure-python)。

### Invoke an existing agent：`agent_reference`

下列片段放在上面 `with` 區塊內，沿用已建立的 `openai_client`；前提是同一 project 已有 `faq-agent`。這段會建立一個 conversation，但不建立新的 agent。

```python
conversation = openai_client.conversations.create(
    items=[
        {
            "type": "message",
            "role": "user",
            "content": "How do I reset my password?",
        }
    ]
)

response = openai_client.responses.create(
    conversation=conversation.id,
    extra_body={
        "agent_reference": {
            "name": "faq-agent",
            "type": "agent_reference",
        }
    },
)
print(response.output_text)
```

**`agent_reference` 指向 agent；`model="faq-agent"` 則會把字串當作模型部署識別，不是在選 agent。** 來源：[Runtime components](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/runtime-components?tabs=python)。

### Current SDK shortcut｜預先綁定 agent

目前 quickstart 也示範 `get_openai_client(agent_name=...)`：建立已綁定 agent 的 client，後續不必每次手動填入 `agent_reference`。下面仍放在前面 `project_client` 的有效範圍內。

```python
with project_client.get_openai_client(agent_name="faq-agent") as agent_client:
    conversation = agent_client.conversations.create()
    response = agent_client.responses.create(
        conversation=conversation.id,
        input="How do I reset my password?",
    )
    print(response.output_text)
```

兩種路徑都在呼叫已存在的 agent。舊題若在 `model=agent_name` 和 `agent_reference` 之間選，仍選後者；新的 bound client 寫法不會讓舊答案失效。來源：[Current Foundry SDK quickstart](https://learn.microsoft.com/en-us/azure/foundry/quickstarts/get-started-code#chat-with-an-agent)。

### Prompt/API shapes：避免把兩套欄位混寫

| API／做法 | Input / instructions | Output | 本模組要記住 |
| --- | --- | --- | --- |
| **Responses API** | `input`，可用 `instructions` 指定行為 | Response items；文字便利欄位 `output_text` | `responses.create(...)` |
| **Chat Completions API** | `messages` 中的 role/content | `choices` 中的 message | 不要把 `choices[0].message` 當 Responses 結構 |
| **Persisted Foundry agent** | Bound agent client，或 Conversation／input＋`agent_reference` | Agent 執行的 response | 重用已建立的 instructions/tools |
| **Ephemeral agent** | 在應用程式請求中指定 model、instructions、tools | 這次執行的 response | 定義放程式碼，不是持久化 agent 資源 |

Ephemeral 是有效的應用方式，但需求若是「呼叫既有 agent」，便不應改成建立另一套定義。來源：[Responses API](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/responses)、[Ephemeral agents quickstart](https://learn.microsoft.com/en-us/azure/foundry/agents/quickstarts/responses-api)。

### Unified model invocation：為何是 `responses.create()`

**Responses API** 是 Foundry 目前的統一模型呼叫介面；題目若明說 `unified model invocation API`，應想到 `responses.create()`。它用 `input=` 傳單一 prompt 或多模態內容，並可搭配 tools、structured output 與 response state。這不表示 **Chat Completions API** 已不能使用；兩者是不同 API 形狀，應依題幹指定的 API 作答。

本模組程式已做語法檢查，未呼叫 Azure。語音、影像、Content Understanding 的 request payload 放在各服務模組，不在此重複。

## 5. Comparison / Common Confusions

### File Search vs Code Interpreter vs Azure AI Search

| Tool / service | 核心用途 | Input → Output | 適合的情境 |
| --- | --- | --- | --- |
| **File Search** | 搜尋上傳文件內容以做 grounding | Documents＋query → relevant passages／citations | 問 PDF、DOCX、MD 文件中的規則 |
| **Code Interpreter** | 在沙箱內執行 Python，分析資料、計算、產生檔案 | CSV／支援檔案＋指示 → analysis／chart／file | 算平均、整理表格、畫圖 |
| **Azure AI Search tool** | 查詢既有 Search index | Query＋configured index → search results | 已有企業資料索引，希望 agent 使用 |
| **Foundry IQ knowledge base** | 組織一個或多個 knowledge sources 的檢索 | Query → retrieved data／references | 共用多來源知識層；權限依來源整合方式配置 |

File Search 自動處理 parsing、chunking、embeddings 與文字／向量檢索；Code Interpreter 的重點是計算，不能因兩者都能接檔案就混用。來源：[File Search](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/file-search)、[Code Interpreter](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/code-interpreter)、[Foundry IQ FAQ](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/foundry-iq-faq)。

| Original note file format | File Search | Code Interpreter | Current clarification |
| --- | --- | --- | --- |
| **DOC / DOCX** | 兩者列於支援清單 | DOCX 列於清單 | 不把 `.docx` 自動擴大成所有 Word 副檔名 |
| **PDF** | 支援 | 支援 | 選工具仍看搜尋 vs 計算需求 |
| **TXT / MD** | 支援 | 支援 | 純文字與 Markdown |
| **JSON** | 支援 | 支援 | 格式本身不決定用途 |
| **PPTX** | 支援 | 支援 | 以文件內容處理為準 |
| **CSV** | **未列於 File Search 支援格式** | 支援 | 分析表格可用 Code Interpreter；不能推論 Azure 完全不能搜尋 CSV |
| **Excel `.xlsx`** | 未列於 File Search 支援格式 | **支援 `.xlsx`** | 原稿籠統寫 Excel；舊 `.xls` 不應直接套用這項結論 |

格式清單核對日期：**2026-09-06**。依據：[File Search supported types](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/file-search#supported-file-types)、[Code Interpreter supported types](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/code-interpreter#supported-file-types)。Azure AI Search 的 Blob indexer 可處理 CSV，亦可將每列映射成 search document；這和 Agent File Search 的格式限制是不同管道。來源：[Azure Blob indexer](https://learn.microsoft.com/en-us/azure/search/search-how-to-index-azure-blob-storage)。

### Deployment option vs deployment type

| Level | Name | 繁體中文解釋 | Status / trigger |
| --- | --- | --- | --- |
| 無需建立 deployment 的存取方式 | **Instant access** | 對支援的模型直接依名稱推論 | **Preview**；try model、no deployment |
| Deployment **option** | **Serverless API** | 以代管 API 使用 Foundry Models | **Current**；下層有 Standard、Provisioned、Batch 等 types |
| Deployment **option** | **Managed compute** | Foundry 管理的專用 GPU 容量執行支援的 open／custom-weight models | 本文指 **Foundry 新體驗 Preview**；dedicated GPU、custom weights |
| Serverless deployment **type** | **Standard** family | 按 token 使用量計費 | **Current**；variable／bursty traffic |
| Serverless deployment **type** | **Provisioned** family | 預留模型處理容量，以 **PTU** 表示 | **Current**；reserved capacity、predictable throughput |
| Serverless deployment **type** | **Batch** family | 非即時、非同步的大量請求 | **Current**；large asynchronous jobs；依模型支援 |

**原筆記更正：** 舊表把 **“Standard deployment in Foundry resources”** 當成與 managed compute 對比的 option。現行英文總覽使用 **Serverless API vs Managed compute**；**Standard** 是 serverless 下的 type。內容過濾／治理不是只有 Standard 才有，不能用「需要 content filtering」直接排除所有 Provisioned types。來源：[Deployment overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/deployments-overview)、[Deployment types](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/concepts/deployment-types)。

| Processing scope | 現行英文文件的說法 | Exam distinction |
| --- | --- | --- |
| **Global** | 可在全球 Azure 區域處理推論 | 資源建立地點不等於唯一推論處理地點 |
| **Data Zone** | 在 Microsoft 指定 data zone 內處理 | Data zone 可包含多個區域 |
| **Standard / Regional Provisioned** | 最新 type 頁面說明為指定 **Azure geography** 內處理，營運上可能跨該 geography 的區域 | 不要只背「Standard 一定只在單一 region」 |

**文件差異（2026-09-05）：** Deployment overview 仍有 **“Standard (single region)”** 的簡稱；較專門的 [Deployment types／Data residency](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/concepts/deployment-types) 已明示 geography 與跨區域可能性。本庫保留差異並採較具體的最新說明；資料儲存位置與推論處理位置也要分開理解。

### Knowledge mechanisms｜不要只看「knowledge base」就選同一服務

| Pair | 差異／記法 |
| --- | --- |
| **Knowledge base vs knowledge source** | Base 組織檢索設定與來源；source 是所連結的 indexed／remote 內容 |
| **File Search vs custom question answering** | 前者檢索文件片段供 agent 生成；後者維護可回答的 question-answer pairs，不能把格式限制互相套用 |
| **Foundry IQ vs generic knowledge base** | IQ 是特定知識層；舊教材的 knowledge base 也可能只是 FAQ／Q&A 專案 |

來源：[Foundry IQ FAQ](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/foundry-iq-faq)、[Custom question answering format guidelines](https://learn.microsoft.com/en-us/azure/ai-services/language-service/question-answering/reference/document-format-guidelines)。

## 6. Current vs Legacy

| 名詞／能力 | Status | 現行讀法與保留方式 |
| --- | --- | --- |
| **Microsoft Foundry** | **Current** | 目前平台名稱；舊教材的 Azure AI Foundry／Azure AI Studio 要註明版本背景 |
| **Azure AI Projects 2.x examples** | **Current**；stable 版本可含個別 **Preview** 能力 | 本文已改核對 2.5.0 stable；保留舊 2.0.0b4 註記只為避免誤讀 preview view |
| **Threads / runs 的舊 agent samples** | **Legacy exam-bank context** | 與本文 Conversations／Responses 分開；舊 API 名稱不自動等於全服務 Retired |
| **Standard deployment 作為 option 的舊表述** | **Legacy exam-bank context** | 現行 option 是 Serverless API；Standard 為其 type |
| **Foundry managed compute** | 新體驗 **Preview** | 不把其狀態套用到所有 Azure ML managed endpoints |
| **Instant access** | **Preview** | 僅支援模型可免建 deployment，不是全部模型都免部署 |
| **Foundry IQ / agentic retrieval** | **Current＋Preview**，依能力／API | 不整個貼成 GA 或整個貼成 Preview；portal 差異見前文 |
| **Knowledge mining / Azure Cognitive Search** | 歷史名詞／**Legacy exam-bank context** | 現行 Search 能力仍有用途；歷史與 adjacent 深度內容見 [module 08](08_Legacy_and_Adjacent_Topics.md) |

狀態來源：[Current platform map](https://learn.microsoft.com/en-us/azure/foundry/concepts/capabilities)、[SDK versioned reference](https://learn.microsoft.com/en-us/python/api/overview/azure/ai-projects-readme?view=azure-python)、[Deployment overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/deployments-overview)、[Foundry IQ](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-foundry-iq)。

### Older-question wording｜舊題怎麼讀

| Current terminology | Legacy / exam-bank terminology | What to answer if this wording appears on an older question |
| --- | --- | --- |
| **Serverless API** deployment option；其下再選 Standard／Provisioned 等 type | **Standard deployment in Foundry resources** 被當作 option | 舊題問 Azure OpenAI chat app＋guardrails，且選項如此區分時，選該舊 option；不是宣稱 Provisioned 不能套 guardrails |
| **Foundry File Search**、**Foundry IQ**、**custom question answering** 各是不同機制 | 題幹寫 Foundry Agent，解說仍寫 Azure AI Bot Service／generate Q&A pairs | 原題 DOC／PDF 的答案可保留；不要推論所有 Foundry 知識來源都不能用 CSV／ZIP |
| Explicit `agent_reference` 或預綁定 agent 的 client | 選項要求 `responses.create()` 加 `agent_reference` | 呼叫已存在 agent 時該選項仍正確；不能把 agent 名稱填在 `model` |

Custom question answering 目前標為 **Retiring**，此處只為解讀舊題保留名稱；服務詳情見 [Text and Language](04_Text_and_Language.md)。來源：[CQA overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/question-answering/overview)、[Deployment overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/deployments-overview)、[Runtime components](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/runtime-components?tabs=python)。

## 7. Exam Keywords & Triggers

| English trigger | 應想到 | 需要排除的誤解 |
| --- | --- | --- |
| **System prompt / instructions** | 角色、範圍、規則、輸出格式 | 不是當次 user 問題 |
| **Few-shot** | Prompt 中提供輸入輸出範例 | 不是訓練新模型 |
| **Existing agent / agent name** | `agent_reference` | 不是 `model=agent_name` |
| **Project endpoint / lightweight chat client** | `AIProjectClient` → OpenAI client → Responses | Endpoint 不是 deployment name |
| **PDF / DOC content search** | File Search | 不是 CSV 計算器 |
| **CSV / calculate average / chart** | Code Interpreter | 只檢索文字不等於完成計算 |
| **Existing search index** | Azure AI Search tool | 不一定要重新上傳 File Search |
| **Multiple knowledge sources** | Knowledge base／Foundry IQ | 不是所有來源都複製進索引 |
| **PTU / reserved capacity** | Provisioned type | 和 Standard 是同一層的 type 比較 |
| **No deployment / quickly try** | Instant access，Preview | 不可套到任意模型 |
| **Dedicated GPU / custom weights** | Managed compute option | 不等於 Provisioned PTU |

## 8. Memory Rules

| English memory rule | 繁體中文記法 |
| --- | --- |
| **Catalog selects; deployment serves** | Catalog 挑模型，deployment 提供推論 |
| **Model + instructions + tools = agent building blocks** | 模型會推理，指示定規則，工具連動作 |
| **Project → OpenAI client → response** | 先接專案，再拿 client，再呼叫 |
| **Agent reference is not a model name** | Agent 名稱放 agent_reference |
| **File Search retrieves; Code Interpreter computes** | 文件查內容；資料跑計算 |
| **Index stores; indexer loads** | Index 可查，indexer 搬資料 |
| **Hybrid combines; semantic ranker reorders** | 混合合併，語意重排 |
| **Option first, type second** | 先看 API／GPU 路徑，再看 Standard／Provisioned 等 type |

## 9. Official Sources

以下英文 Microsoft 官方來源於 **2026-09-05 至 2026-09-06** 核對；工具格式、模型可用性和 Preview 狀態需要持續複查。

| Source | 本模組用途 |
| --- | --- |
| [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) | Portal、prompts、SDK、single-agent 的範圍 |
| [Foundry capability map](https://learn.microsoft.com/en-us/azure/foundry/concepts/capabilities) | 平台、模型、tools、knowledge 分工 |
| [Foundry Models overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/foundry-models-overview) | Model catalog 與模型選擇 |
| [Safety system messages](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/system-message) | 系統提示的組成與限制 |
| [Prompt engineering techniques](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/prompt-engineering) | 清楚指示、few-shot、格式與 context |
| [RAG prompt engineering](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/rag/rag-prompt-engineering) | 檢索資料如何加入 prompt |
| [AI agents](https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/7-agents) | Model、instructions、tools |
| [Azure AI Projects Python reference](https://learn.microsoft.com/en-us/python/api/overview/azure/ai-projects-readme?view=azure-python) | 2.5.0 stable SDK、client、Entra 認證、個別 Preview 限制 |
| [Foundry SDK quickstart](https://learn.microsoft.com/en-us/azure/foundry/quickstarts/get-started-code) | 目前 2.x 用法與 bound agent client |
| [Runtime components](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/runtime-components?tabs=python) | agent_reference、Conversations、Responses、tool output |
| [Responses API](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/responses) | 直接呼叫部署模型、回傳結構 |
| [Responses ephemeral agent quickstart](https://learn.microsoft.com/en-us/azure/foundry/agents/quickstarts/responses-api) | 程式碼中定義的 ephemeral agents |
| [File Search](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/file-search) | 文件檢索行為與支援副檔名 |
| [Code Interpreter](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/code-interpreter) | Python 分析、CSV／XLSX／JSON 等格式 |
| [Azure AI Search overview](https://learn.microsoft.com/en-us/azure/search/search-what-is-azure-search) | Search、indexing、querying、RAG |
| [Indexer overview](https://learn.microsoft.com/en-us/azure/search/search-indexer-overview) | Pull ingestion、skillsets、enrichment |
| [Hybrid search](https://learn.microsoft.com/en-us/azure/search/hybrid-search-overview) | Full-text＋vector、RRF |
| [Semantic ranking](https://learn.microsoft.com/en-us/azure/search/semantic-search-overview) | Reranking 與 extractive answers |
| [Azure Blob indexer](https://learn.microsoft.com/en-us/azure/search/search-how-to-index-azure-blob-storage) | CSV 及 Office 檔案索引支援 |
| [What is Foundry IQ](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-foundry-iq) | Indexed／remote sources、GA／Preview 限定 |
| [Foundry IQ FAQ](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/foundry-iq-faq) | Knowledge bases、Search 相依、可選 LLM、權限 |
| [Custom question answering formats](https://learn.microsoft.com/en-us/azure/ai-services/language-service/question-answering/reference/document-format-guidelines) | 舊 Q&A 知識庫題型與目前 File Search 的區別 |
| [Custom question answering overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/question-answering/overview) | Retiring 狀態；只保留舊題必要背景 |
| [Deployment overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/deployments-overview) | Serverless API、Managed compute、Instant access |
| [Deployment types](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/concepts/deployment-types) | Standard、Provisioned、Batch、data processing scope |
