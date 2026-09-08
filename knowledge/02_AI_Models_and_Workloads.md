# AI Models and Workloads｜AI 模型與工作負載

驗證日期：**2026-09-06**。英文考試先辨認 **workload → input/output → model capability → configuration**；傳統 ML 評估是相關基礎，現行考綱優先讀生成式模型、能力選擇與設定。

## 1. Module Overview

| 欄位 | 重點 |
| --- | --- |
| Official service/module name | AI model components and configurations；AI workloads |
| AI-901 relevance (High/Medium/Low) | **High**：generative AI、model selection、configuration、workload selection；**Medium**：傳統 ML lifecycle 與基本 metrics |
| Current status (Current/Legacy/Mixed) | **Current**：模型與評估概念仍有效；考綱相關性不等於產品生命週期 |
| Main exam workloads | Generative AI、agentic AI、text analysis、speech、computer vision、information extraction |
| Primary Microsoft products | Microsoft Foundry、Foundry Models、Foundry Tools；Azure Machine Learning 作補充背景 |

目前 AI-901 要能辨認生成式模型的運作、能力、部署與參數，以及文字、聲音、影像、資訊擷取等工作負載。部署和 prompts 實作集中於 [Microsoft Foundry](03_Microsoft_Foundry.md)。範圍依據：[AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)。

## 2. Core Concepts

### Generative model vocabulary｜生成式模型詞彙

| English term | 繁體中文解釋與例子 | 考試區分 |
| --- | --- | --- |
| **Generative AI / LLM** | 依提示與學得的語言模式生成內容；LLM 是大型語言模型 | 生成流暢不等於內容已查證 |
| **Small Language Model (SLM)** | 較精簡的語言模型，是否適合仍取決於任務表現 | 模型大小不是唯一選擇條件 |
| **Token / Tokenization** | 把文字切成字詞、子詞、標點等處理單位 | Token 不等於固定一個單字或中文字 |
| **Embedding** | 表示語意的數值向量，可用於相似度檢索 | 輸出是向量，不是聊天回答 |
| **Transformer / Attention** | 依上下文關係影響 token 表示與下一 token 預測 | 不把所有模型架構都簡化成同一組 encoder/decoder |
| **Context window** | 模型一次可使用的上下文容量，需包含輸入與輸出預算 | Reasoning tokens 也可能占用容量 |
| **Multimodal model** | 支援多種資料形式；例如 text＋image → text | 能讀圖片不代表能生成圖片；逐模型查 input/output |

Token、attention、embeddings 與生成依據：[Large language models](https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/3-language-models)。上下文與模態限制見 [Reasoning models](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/reasoning)；[模型比較文件](https://learn.microsoft.com/en-us/azure/foundry/how-to/benchmark-model-in-catalog) 的 leaderboard 功能標為 **Preview**。

### Workload selection：先看輸出，不只看輸入

| Workload | Input | Output | 真實情境與英文關鍵字 |
| --- | --- | --- | --- |
| **Generative AI** | Prompt＋可選背景資料 | 新文字、程式碼或其他生成內容 | 為商品撰寫說明：**generate, draft, create** |
| **Agentic AI** | 使用者目標＋環境／工具資訊 | 工具呼叫、行動結果、回覆 | 查詢訂單後調用服務完成任務：**tools, act, automate** |
| **Text analysis** | 非結構化文字 | Entities、key phrases、sentiment、summary | 分析客服留言：**extract entities, sentiment** |
| **Speech recognition** | Spoken audio | Transcript | 會議錄音轉文字：**speech-to-text, transcribe** |
| **Speech synthesis** | Text | Synthesized speech | 把通知讀出來：**text-to-speech, voice** |
| **Computer vision** | Image／video | Labels、位置、描述、文字等分析結果 | 找出照片物件：**classify, detect, OCR** |
| **Image generation** | 文字描述或支援的編輯輸入 | 新圖片 | 建立海報插圖：**create visual output** |
| **Information extraction** | Document、image、audio、video | 擷取內容與指定 schema 的結構化欄位 | 合約日期、通話摘要、影片欄位：**fields, schema, structured output** |

這張表是考綱的工作負載導航，不代表每個模型都支援所有輸出。Agent 的 **model＋instructions＋tools** 組合依據：[AI agents](https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/7-agents)。服務細節見 [Text and Language](04_Text_and_Language.md)、[Speech](05_Speech.md)、[Computer Vision](06_Computer_Vision.md)、[Content Understanding](07_Content_Understanding.md)。

### Traditional machine learning｜相關基礎（Medium）

| Concept | Input → Output | 繁體中文例子 | Exam distinction |
| --- | --- | --- | --- |
| **Feature** | 模型輸入欄位 | 房屋面積、房間數、地區 | 用來預測的資訊 |
| **Label / Target** | 訓練資料中的正確答案 | 已知成交價格 | 監督式學習要學會預測的目標 |
| **Regression** | Features → numeric value | 預測房價、租車需求量 | **How much / how many** |
| **Binary classification** | Features → 兩種類別之一 | 垃圾郵件／非垃圾郵件 | **Yes/no, two classes** |
| **Multiclass classification** | Features → 多個類別之一 | 物品分為書籍／服飾／食品 | 一筆最終選一類；不等於 multilabel |
| **Clustering** | 未標註特徵 → 相似群組 | 依購買行為把客戶分群 | **Group similar items**；預先沒有目標類別標籤 |
| **Training** | 資料＋學習演算法 → 模型 | 從歷史房價學習規律 | 建立／調整模型參數 |
| **Inference** | 已有模型＋新輸入 → 預測 | 輸入一間新房屋，取得預估價格 | 使用模型；不是每次都重新訓練 |

依據：[Introduction to machine learning concepts](https://learn.microsoft.com/en-us/training/modules/fundamentals-machine-learning/)。影像的 multiclass、multilabel、object detection 詳見 [Computer Vision](06_Computer_Vision.md)；Azure ML workspace、datastore、compute、Designer、AutoML 與 Monte Carlo CV 放在 [Legacy and Adjacent Topics](08_Legacy_and_Adjacent_Topics.md)。

## 3. How It Works

### Language generation

```text
Instructions + user prompt + available context
→ tokenize and represent tokens
→ use contextual relationships to predict the next token
→ select a token under supported generation settings
→ repeat until a stop condition or output limit
→ return generated content
```

模型生成時使用請求可用的上下文與已學得的模式；要回答公司最新政策，通常需由應用程式提供檢索資料。**Grounding** 是把回覆建立在提供的資料上；**Retrieval-Augmented Generation (RAG)** 把檢索結果加入生成上下文，不是在每次查詢重新訓練權重。來源：[LLM 運作](https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/3-language-models)、[RAG prompt engineering](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/rag/rag-prompt-engineering)。

### ML lifecycle｜資料、訓練、評估、部署（Medium）

| Step | 做什麼 | Input → Output | 房價例子 |
| --- | --- | --- | --- |
| **1. Prepare data** | 清理缺失、錯誤與格式，整理可使用特徵 | 原始資料 → 訓練／驗證／測試所需資料 | 統一面積單位，處理缺漏欄位 |
| **2. Train a model** | 用訓練資料讓演算法學習 | Training features＋labels → model | 學到面積與房價等關係 |
| **3. Evaluate a model** | 用未參與訓練的資料比較預測與實際結果 | Predictions＋ground truth → metrics | 計算 MAE／RMSE，判斷誤差 |
| **4. Deploy a model** | 把模型提供給應用程式推論 | Model → callable inference endpoint | App 輸入房屋資訊後得到價格 |

**Validation data** 用於開發時比較設定；**test data** 保留作最後評估，不能反覆拿測試集調參而仍宣稱它是獨立測試。基本流程與評估依據：[ML training concepts](https://learn.microsoft.com/en-us/training/modules/fundamentals-machine-learning/)、[Train and evaluate a model](https://learn.microsoft.com/en-us/dotnet/machine-learning/how-to-guides/train-machine-learning-model-ml-net)。

### Model evaluation：保留原圖並補充限制

![原始模型評估投影片：Confusion Matrix、Precision、Recall、F1、MAE](../assets/knowledge/02_model_evaluation.png)

**更正／補充（Current，2026-09-05）：** 圖中的 **2×2 confusion matrix** 適用二元分類；多類別可用更大的矩陣。**MAE 是回歸誤差指標之一**，不是看到 regression 就只能選 MAE；還可能是 MSE、RMSE、R²。Precision／Recall 的疾病比喻只協助理解分母，實際用途須一起衡量誤判與漏判。來源：[Azure ML evaluation](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-understand-automated-ml?view=azureml-api-2)、[ML.NET metrics](https://learn.microsoft.com/en-us/dotnet/machine-learning/resources/metrics)。

| Binary confusion matrix | Actual positive | Actual negative |
| --- | --- | --- |
| **Predicted positive** | **TP — True Positive**：正確抓到正例 | **FP — False Positive**：誤把負例當正例 |
| **Predicted negative** | **FN — False Negative**：漏掉正例 | **TN — True Negative**：正確排除負例 |

以下公式假設分母非零；正類是應用程式事先定義的目標，例如「垃圾郵件」。

| Metric | 定義／公式 | 在問什麼 | 較佳方向 |
| --- | --- | --- | --- |
| **Accuracy** | `(TP + TN) / (TP + FP + FN + TN)` | 所有案例判對多少？ | 高；類別不平衡時要搭配其他指標 |
| **Precision** | `TP / (TP + FP)` | 被你判成正類的裡面，多少是真的？ | 高；關心 **false positives** |
| **Recall / Sensitivity** | `TP / (TP + FN)` | 真正的正類裡面，抓到多少？ | 高；關心 **false negatives** |
| **F1 score** | `2 × precision × recall / (precision + recall)` | Precision 與 Recall 的調和平均 | 高；不包含 TN，不能取代所有指標 |
| **Mean Absolute Error (MAE)** | `mean(abs(actual - predicted))` | 數值平均差多少？ | 低；單位與預測目標相同 |
| **Mean Squared Error (MSE)** | `mean((actual - predicted)²)` | 平方誤差平均，較強調大誤差 | 低；單位為目標單位的平方 |
| **Root Mean Squared Error (RMSE)** | `sqrt(MSE)` | 把平方誤差轉回原目標單位 | 低 |
| **R-squared (R²)** | 相對於平均值基準的解釋／預測表現 | 模型比平均值基準改善多少？ | 越接近 1 越好；**可能為負數** |

自建示例：垃圾郵件分類有 TP=80、FP=20、FN=10、TN=890，Precision=`80/100=80%`、Recall=`80/90≈88.9%`、Accuracy=`970/1000=97%`。高 Accuracy 仍可能掩蓋重要錯誤；不要把三個分母混在一起。指標定義依據：[Azure ML evaluation](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-understand-automated-ml?view=azureml-api-2)、[ML.NET metrics](https://learn.microsoft.com/en-us/dotnet/machine-learning/resources/metrics)。

## 4. Important API / SDK Patterns

### Model configuration：名稱與 API 家族一起記

| Setting / field | 控制什麼 | Example / Output effect | 常見陷阱 |
| --- | --- | --- | --- |
| **`model`** | 要呼叫的模型部署 | 指向已部署的模型 | 在本庫的 Azure deployment 範例中是 **deployment name**，不一定等於 model family |
| **`temperature`** | 支援時，調整取樣隨機程度 | 較低通常較集中，較高通常更多樣 | 低溫不是事實查核，也不能宣稱所有模型都可設定 |
| **`top_p`** | Nucleus sampling：限制候選 token 的累積機率質量 | 較低縮小可選集合 | 通常調 temperature 或 top_p 其中之一；不是字數比例 |
| **`max_output_tokens`** | Responses API 的輸出 token 預算 | 限制最多生成量；可能含 reasoning tokens | 不等於「一定生成這麼多」，也不等於中文字數 |
| **`max_completion_tokens`** | 支援模型的 Chat Completions 輸出預算欄位 | 含 reasoning 與可見回答的 completion 上限 | 不要把 Responses 的欄位名直接搬來 |
| **`reasoning={"effort": "medium"}`** | 支援的 reasoning model 推理投入程度 | 可能影響延遲、品質與 token 使用 | Responses 用巢狀物件；Chat Completions 用 `reasoning_effort`；值因模型而異 |
| **Instructions / system prompt** | 行為、角色、範圍與格式規則 | 要求根據提供資料答題，證據不足時說明 | 不會替模型新增感官能力或資料存取權 |

欄位依據：[Responses REST reference](https://learn.microsoft.com/en-us/rest/api/microsoft-foundry/azureopenai/responses?preserve-view=true&view=rest-microsoft-foundry-v1-preview)、[Reasoning model guidance](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/reasoning)。Temperature／top_p 定義與不要同時調整兩者的建議見 [Azure OpenAI connector reference](https://learn.microsoft.com/en-us/connectors/azureopenai/)；這個來源用來核對參數概念，**不是本庫 Python 範例的 API**。

**Current vs Preview 必須逐項看：** REST reference 上方的 `v1-preview` view 不代表所有 Responses 功能皆為 Preview；也不能因 Responses 可用，就推定所有模型支援所有參數。SDK 初始化、認證和可執行程式骨架放在 [Foundry API / SDK Patterns](03_Microsoft_Foundry.md#4-important-api--sdk-patterns)，本節不重複另一套 client。

## 5. Comparison / Common Confusions

### Model selection checklist

| 比較維度 | 先問什麼 | 選擇例子 |
| --- | --- | --- |
| **Modality and task** | 輸入／輸出形式與工具能力符合嗎？ | 聲音輸入需 audio-capable model；產圖需 image-generation capability |
| **Quality** | 在自己的代表性資料上表現如何？ | 以真實客服問句比較正確性、完整性 |
| **Latency / Throughput** | 等多久？同時多少請求？ | 即時對話與隔夜批次可接受的延遲不同 |
| **Cost** | 每次任務的輸入、輸出和所需容量成本？ | 在品質達標後比較成本，不只比較模型大小 |
| **Context window** | 文件＋歷史＋答案是否放得下？ | 長合約需要適當窗口，或切分檢索 |
| **Availability / Governance** | 區域、配額、部署、資料處理限制是否符合？ | 功能符合但目標區域不可部署，仍不能直接採用 |

比較維度依據：[Model benchmarks](https://learn.microsoft.com/en-us/azure/foundry/concepts/model-benchmarks)、[Model deployment options](https://learn.microsoft.com/en-us/azure/foundry/concepts/deployments-overview)。部署層次與資料地理範圍見 [Foundry comparison](03_Microsoft_Foundry.md#5-comparison--common-confusions)。

| 容易混淆的概念 | 正確區分 |
| --- | --- |
| **Generation vs retrieval** | 生成新內容 vs 找到既有資料；RAG 把兩者接起來 |
| **Embedding vs chat completion** | 數值向量 vs 給人的回答；向量不能直接當 agent 回覆 |
| **Inference settings vs learned weights** | 本次生成設定 vs 訓練學得的權重；改 temperature 不會重新訓練模型 |
| **Prompting vs fine-tuning** | 修改輸入指示／範例 vs 用訓練資料調整模型；缺少最新資料時先考慮 retrieval |
| **Model vs deployment vs agent** | 能力本體 vs 可呼叫的部署 vs 結合模型、指示與工具的應用 |
| **Multiclass vs multilabel** | 多類別擇一 vs 一筆可同時具有多個標籤 |
| **Precision vs Recall** | 分母是「預測為正」vs「實際為正」 |

Prompting、fine-tuning、retrieval 的選擇依據：[Foundry capability map](https://learn.microsoft.com/en-us/azure/foundry/concepts/capabilities)。

## 6. Current vs Legacy

| Material / term | Status | AI-901 relevance | 維護方式 |
| --- | --- | --- | --- |
| Generative models、modalities、configuration | **Current** | High | 以當前 model card 與英文 API 說明為準 |
| 傳統 ML lifecycle、classification/regression metrics | **Current** | Medium | 與模型理解相關；目前 skills measured 未逐項列出公式，優先級低於生成式實作 |
| Azure ML resource glossary、Designer／AutoML 操作 | 個別產品狀態見 module 08 | Low／Adjacent | [Legacy and Adjacent Topics](08_Legacy_and_Adjacent_Topics.md) 集中保存 |
| 固定模型排行榜、容量、價格與版本清單 | 時間敏感，非永久事實 | High 的選擇方法 | 不把原投影片的型號、模型數量當成可用性保證 |

狀態與相關性是兩個維度：**Low relevance ≠ Retired**。目前範圍以 [英文 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) 為準。

## 7. Exam Keywords & Triggers

| English trigger | 第一個想到的概念 | 再確認 |
| --- | --- | --- |
| **Generate / compose / draft** | Generative AI | 所需輸出是文字還是圖片？ |
| **Take actions / use tools** | Agentic AI | 模型之外的工具與指示 |
| **Semantic similarity / numeric vector** | Embeddings | 用於檢索／相似度，不是直接回答 |
| **Randomness / diverse responses** | Temperature、top_p | 該模型是否允許設定？ |
| **Maximum generated tokens** | 輸出 token 上限 | Responses 與 Chat Completions 欄位名不同 |
| **Predict a numeric value** | Regression | MAE／RMSE 等合適指標 |
| **False positives** | Precision | 被判正例的品質 |
| **False negatives / missed positives** | Recall | 真正正例的涵蓋程度 |
| **Balance precision and recall** | F1 | Harmonic mean |
| **Unknown labels / similar groups** | Clustering | 不要誤選監督式分類 |

## 8. Memory Rules

| English memory rule | 繁體中文記法 |
| --- | --- |
| **Input → capability → output** | 先確認讀得進去、做得到、輸出正確形式 |
| **Train learns; inference uses** | 訓練學規律，推論用模型 |
| **Token is a unit, not a word count** | Token 不是固定字數 |
| **Retrieval supplies evidence; generation writes** | 檢索給證據，生成寫答案 |
| **Precision: predicted positives** | 你抓到的，有多少是真的？ |
| **Recall: actual positives** | 真正有的，你抓到多少？ |
| **Errors down; useful scores up** | MAE／MSE／RMSE 越低越好；Precision／Recall／F1 越高越好 |

## 9. Official Sources

以下英文 Microsoft 官方來源於 **2026-09-05 至 2026-09-06** 核對；範圍、API 支援及 Preview 狀態以連結的最新版為準。

| Source | 本模組用途 |
| --- | --- |
| [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) | 現行工作負載、模型與設定考綱 |
| [Large language models](https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/3-language-models) | Token、embedding、attention、生成 |
| [AI agents](https://learn.microsoft.com/en-us/training/modules/fundamentals-generative-ai/7-agents) | Agent 的模型、指示、工具 |
| [Machine learning concepts](https://learn.microsoft.com/en-us/training/modules/fundamentals-machine-learning/) | Regression、classification、clustering、訓練基礎 |
| [Train and evaluate a model](https://learn.microsoft.com/en-us/dotnet/machine-learning/how-to-guides/train-machine-learning-model-ml-net) | 訓練、測試、預測與評估流程 |
| [Azure ML evaluation metrics](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-understand-automated-ml?view=azureml-api-2) | Confusion matrix、Precision、Recall、F1、回歸誤差 |
| [ML.NET metrics](https://learn.microsoft.com/en-us/dotnet/machine-learning/resources/metrics) | 指標對應任務、R² 範圍、類別不平衡 |
| [Model comparison](https://learn.microsoft.com/en-us/azure/foundry/how-to/benchmark-model-in-catalog) | Model card 與 context window 比較 |
| [Model benchmarks](https://learn.microsoft.com/en-us/azure/foundry/concepts/model-benchmarks) | Quality、latency、throughput、cost |
| [Deployment overview](https://learn.microsoft.com/en-us/azure/foundry/concepts/deployments-overview) | 模型可用性與部署路徑 |
| [RAG prompt engineering](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/rag/rag-prompt-engineering) | Grounding context 與使用者問題 |
| [Foundry capability map](https://learn.microsoft.com/en-us/azure/foundry/concepts/capabilities) | Prompting、retrieval、fine-tuning 選擇 |
| [Azure OpenAI connector reference](https://learn.microsoft.com/en-us/connectors/azureopenai/) | Temperature 與 top_p 的定義 |
| [Responses REST reference](https://learn.microsoft.com/en-us/rest/api/microsoft-foundry/azureopenai/responses?preserve-view=true&view=rest-microsoft-foundry-v1-preview) | Responses 欄位；版本視圖含 Preview 功能 |
| [Reasoning models](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/reasoning) | Reasoning effort 與模型特定參數限制 |
