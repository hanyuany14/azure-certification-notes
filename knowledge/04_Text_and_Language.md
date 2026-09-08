# Text and Language｜文字分析與語言

查核日期：**2026-09-06** · 以英文 Microsoft Learn 為準 · [學習索引](../README.md)

## 1. Module Overview

| Item | Study notes |
| --- | --- |
| Official service/module name | **Azure Language in Foundry Tools**；本模組涵蓋 Text analysis / Natural Language Processing (NLP) |
| AI-901 relevance | **High**：keyword extraction、entity detection、sentiment analysis、summarization，以及輕量文字分析應用；Language detection / CLU：**Medium**；Entity linking / Translator：**Adjacent** |
| Current status | **Current** 服務；NER、Language detection 為現行核心功能；Key phrases、Sentiment / Opinion mining、Summarization、CLU、Entity Linking 已公告 **Retiring**，詳見第 6 節 |
| Main exam workloads | 非結構化文字 → 主題、實體、情緒或摘要；由輸入與所需輸出選功能 |
| Primary Microsoft products | Microsoft Foundry、Azure Language；跨語言翻譯另用 **Azure Translator in Foundry Tools** |

範圍：[AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)。服務名稱：[Azure Language overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/overview)。**文字分析技術仍是考試核心；特定 Azure API 將退役，不代表 sentiment、summary 等概念不再考。**表中 High / Medium / Adjacent 是本知識庫依綱要安排的讀書優先度，並非 Microsoft 官方配分。

## 2. Core Concepts

### 文字分析任務（Text analysis tasks）

| English Term | 中文解釋 | What it does | Input | Output | Exam Keywords |
| --- | --- | --- | --- | --- | --- |
| **Key Phrase Extraction** | 找出文章重點片語 | 擷取主要概念，不產生完整摘要 | 評論、文章等文字 | 片語清單 | main topics, keywords, key phrases |
| **Named Entity Recognition (NER)** | 辨識實體並分類 | 找人名、地點、組織、日期等；綱要使用較一般的 entity detection | 非結構化文字 | 實體文字、類別、位置、confidence | person, organization, location, entities |
| **Entity Linking** | 消歧義並連到知識來源 | 根據上下文判斷實體指的是誰／什麼，回傳 Wikipedia 連結 | 含可能歧義名稱的文字 | 已連結實體與 URL | disambiguate, Wikipedia, link |
| **Sentiment Analysis** | 判斷情緒傾向 | 分析句子／文件的正向、負向、中立情緒；文件也可能 mixed | 顧客評論 | sentiment label、confidence scores | positive, negative, neutral, mixed |
| **Opinion Mining** | 找出對特定對象的評價 | 將評價連到 target，例如 service → excellent | 含對象與評價的評論 | target、assessment、情緒關係 | aspect, target, opinion |
| **Extractive Summarization** | 挑選原文重要句 | 保留被選句子的原文措辭 | 長篇文字 | 原文句子子集 | select sentences, preserve wording |
| **Abstractive Summarization** | 用新句子濃縮內容 | 產生精簡摘要，不限定逐字摘錄 | 文字／對話 | 新生成摘要 | concise summary, new wording |
| **Language Detection** | 判斷文字主要語言 | 每份 document 判斷 predominant language | 原始文字 | 語言名稱、ISO code、confidence；依版本含 script | language name, ISO 639-1 |

能力依據：[Key phrases](https://learn.microsoft.com/en-us/azure/ai-services/language-service/key-phrase-extraction/overview)、[NER](https://learn.microsoft.com/en-us/azure/ai-services/language-service/named-entity-recognition/overview)、[Sentiment / opinion mining](https://learn.microsoft.com/en-us/azure/ai-services/language-service/sentiment-opinion-mining/overview)、[Summarization](https://learn.microsoft.com/en-us/azure/ai-services/language-service/summarization/overview)、[Language overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/overview)。表中例子是學習示意，實際抽取結果由模型決定。

### 語句、意圖與實體（Utterance / Intent / Entity）

| English Term | 中文解釋 | What it does | Input | Output | Exam Keywords |
| --- | --- | --- | --- | --- | --- |
| **Utterance** | 使用者實際說／輸入的完整語句 | 提供理解上下文 | 「幫我訂明天去台北的高鐵」 | 作為模型分析輸入 | user's words, sentence |
| **Intent** | 使用者想完成的事 | 對整句話預測意圖 | 上述 utterance | 示意：`BookTrainTicket` | intention, action, goal |
| **Entity** | 執行意圖需要的資訊 | 擷取語句中的重要參數 | 上述 utterance | 明天＝Date；台北＝Destination | details, parameters, slots |
| **Conversational Language Understanding (CLU)** | 訓練自訂語言理解模型 | 預測 intent 並擷取 entities | utterance | 意圖與實體結果 | conversational understanding |

以上實體標籤為自訂專案示意，不代表固定內建分類。CLU 負責理解，不會因為輸出 `BookTrainTicket` 就自行訂票。[CLU overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/conversational-language-understanding/overview)

## 3. How It Works

### 同一段文字，不同的分析問題

輸入示意：`Contoso opened an office in Taipei. The staff were helpful, but the room was dirty.`

| 想問的問題 | 選擇 | 示意結果／考試區別 |
| --- | --- | --- |
| What is this text about? | Key Phrase Extraction | office、staff、room 等重點片語 |
| Who / Where / Which organization? | NER | Contoso → Organization；Taipei → Location |
| How does the writer feel overall? | Sentiment Analysis | 正負情緒並存；不要只看 helpful 一字 |
| What exactly is praised or criticized? | Opinion Mining | staff → helpful；room → dirty |
| Can you shorten this into a readable recap? | Summarization | 一段精簡摘要，而非只有幾個名詞 |

### 語言偵測結果（Language detection output）

| 欄位／概念 | 意義 | 示意值 | 易混淆處 |
| --- | --- | --- | --- |
| `name` | 人類可讀語言名稱 | `English` | 不等於文字主題 |
| `iso6391Name` | ISO 639-1 語言代碼 | `en`, `fr`, `zh` | 不是 Wikipedia URL |
| `confidenceScore` | 模型判斷把握程度，0–1 | `0.99` | 不是翻譯品質、正向情緒機率 |
| Script name / code | 書寫系統及 ISO 15924 代碼 | `Latin` / `Latn` | language 與 writing system 是不同層次 |
| Predominant language | 混合語言文件中占主要比例的語言 | 一份文件回一個主要語言 | 不保證列出每一句所有語言 |
| `countryHint` | 協助消除短詞歧義的國家／地區提示 | `fr` 幫助理解 communication | 提示而非強制指定結果 |

**Current correction — Unknown vs NaN：**目前官方範例對無法辨識的輸入回傳 `name: "(Unknown)"`、`iso6391Name: "(Unknown)"`、`confidenceScore: 0.0`。原題庫的 **NaN** 預期答案保留為 **Legacy exam-bank context**，不能當成目前所有 API 的共同契約。[Language detection behavior](https://learn.microsoft.com/en-us/azure/ai-services/language-service/language-detection/how-to/call-api)

**Version distinction：**上述 how-to 的 JSON 使用 `script` / `scriptCode`；REST **2026-05-01** schema 使用 `scriptName` / `scriptIso15924Code`。以題目指定版本與 SDK 欄位為準，不把不同版本拼成同一份 response。[REST schema](https://learn.microsoft.com/en-us/rest/api/language/analyze-text/analyze-text/analyze-text?view=rest-language-analyze-text-2026-05-01)

## 4. Important API / SDK Patterns

### Language Detection REST：辨識 request 與 response 的位置

下列 Python 只建立 **REST 2026-05-01** 請求資料，沒有執行雲端呼叫。`LANGUAGE_ENDPOINT` 應來自資源設定；金鑰不硬編碼。

```python
import os

url = (
    os.environ["LANGUAGE_ENDPOINT"].rstrip("/")
    + "/language/:analyze-text?api-version=2026-05-01"
)
headers = {"Ocp-Apim-Subscription-Key": os.environ["LANGUAGE_KEY"]}
payload = {
    "kind": "LanguageDetection",
    "analysisInput": {"documents": [
        {"id": "1", "text": "Bonjour tout le monde.", "countryHint": "fr"}
    ]},
}
```

| API pattern | 記法 |
| --- | --- |
| `kind` | 這次要做哪一種分析；上例為 LanguageDetection |
| `analysisInput.documents` | 輸入文字文件集合；用 id 對應結果 |
| `results.documents[].detectedLanguage` | 成功文件的語言分析結果 |
| `results.errors` | 檢查個別文件錯誤，不假定整批皆成功 |
| REST camelCase vs SDK properties | `iso6391Name` 等 JSON 欄位不可直接推論為每種 SDK 的語法；查該 SDK reference |

契約：[Analyze Text REST 2026-05-01](https://learn.microsoft.com/en-us/rest/api/language/analyze-text/analyze-text/analyze-text?view=rest-language-analyze-text-2026-05-01)。本例用明確 API 版本保留可讀性；不代表所有舊資源／SDK 自動支援相同欄位。

### 核心文字分析 API 的辨認方式

| 分析需求 | REST `kind` / 設定 | 主要回傳內容 |
| --- | --- | --- |
| NER | `EntityRecognition` | `entities`：文字、category、offset、length、confidenceScore |
| Key phrases | `KeyPhraseExtraction` | `keyPhrases` 清單 |
| Sentiment | `SentimentAnalysis` | 文件與句子的 sentiment、confidenceScores |
| Opinion mining | `SentimentAnalysis` + `parameters.opinionMining: true` | 句子中的 targets、assessments 與關係 |

這些分析使用帶 `language` 的文字文件輸入；上方 LanguageDetection 範例則使用 `countryHint`，不可盲目照搬欄位。摘要另走非同步分析工作，不在此表的單次 `:analyze-text` 任務清單中。[Analyze Text REST](https://learn.microsoft.com/en-us/rest/api/language/analyze-text/analyze-text/analyze-text?view=rest-language-analyze-text-2026-05-01)、[Summarization workflow](https://learn.microsoft.com/en-us/azure/ai-services/language-service/summarization/overview)

## 5. Comparison / Common Confusions

| Compare | 第一個概念 | 第二個概念 | Exam distinction |
| --- | --- | --- | --- |
| Key phrases vs NER | What is it about? 主題片語 | Who / Where / When? 實體＋分類 | 找重點不等於標人名地名 |
| NER vs Entity linking | 偵測並標類別 | 判斷具體指涉並附知識連結 | URL / Wikipedia / disambiguation 指向 linking；一般 NER 不保證提供 Wikipedia URL |
| Sentiment vs Opinion mining | 情緒分類 | 特定對象的情緒 | 「服務好、房間差」要拆 target |
| Summarization vs Key phrases | 可閱讀的精簡內容 | 重點片語清單 | summary 不是 keywords list |
| Language detection vs Translation | 判斷是哪種語言 | 改寫成目標語言 | `en` 是語言識別結果，不是翻譯 |
| Text language detection vs Speech language identification | 輸入 text | 輸入 audio | 音訊識別見 [Speech](05_Speech.md) |
| Utterance vs Intent vs Entity | 原句 vs 想做的事 vs 必要參數 | 三者層次不同 | 「訂明天台北的票」：完整句／訂票／日期目的地 |

### Translator：保留相鄰服務邊界

| Capability | Input → Output | 例子 | Relevance |
| --- | --- | --- | --- |
| **Text Translation** | 文字 → 目標語言文字 | 英文客服訊息 → 繁體中文 | Adjacent |
| **Document Translation** | 文件 → 翻譯文件，保留結構與格式 | Word / PDF 文件翻譯 | Adjacent |
| **Speech Translation** | 語音 → 另一語言文字／語音 | 即時口語翻譯 | Medium；屬 Azure Speech |

修正原筆記邊界：**Azure Translator** 是翻譯服務，不因為輸入是文字就統稱 Azure Language。[Translator overview](https://learn.microsoft.com/en-us/azure/ai-services/translator/overview)、[Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/overview)

## 6. Current vs Legacy

| Term / capability | Status / official classification | 保留與修正 |
| --- | --- | --- |
| Azure Language in Foundry Tools | **Current** | 目前服務名稱；歷史路徑／SDK 名稱可能仍保留 Language / Text Analytics 字樣 |
| NER、Language detection | **Current**；overview 的 core capabilities | 不因部分舊教材也介紹就稱 retired |
| Key phrases、Sentiment / Opinion mining、Summarization | **Retiring**；也列於 legacy capabilities | 功能專頁已公告 2029-03-31 退役；保留分析技術與辨讀舊 API 的必要知識；新建工作負載依官方指引評估 Foundry |
| CLU | **Retiring** | 同樣公告 2029-03-31 退役；保留 utterance / intent / entity，不能把「LUIS → CLU」當成永久有效的新建建議 |
| Entity Linking | **Retiring** | 公告 2028-09-01 退役；保留消歧義／Wikipedia URL 題型。官方指向 NER 或 Foundry，但 NER 不是保證回 URL 的同介面替代 |
| LUIS | **Legacy exam-bank context** | 保留 utterance / intent / entity；歷史名稱及退役脈絡集中在 [Legacy and Adjacent](08_Legacy_and_Adjacent_Topics.md) |
| Language detection unknown → NaN | **Legacy exam-bank context** | 舊題庫預期不覆蓋目前官方 0.0 範例；不可猜測未指定版本的 NaN 行為 |

**生命週期來源優先看功能專頁的明確公告**：[Key phrases](https://learn.microsoft.com/en-us/azure/ai-services/language-service/key-phrase-extraction/overview)、[Sentiment / opinion mining](https://learn.microsoft.com/en-us/azure/ai-services/language-service/sentiment-opinion-mining/overview)、[Summarization](https://learn.microsoft.com/en-us/azure/ai-services/language-service/summarization/overview)、[CLU](https://learn.microsoft.com/en-us/azure/ai-services/language-service/conversational-language-understanding/overview)、[Entity Linking](https://learn.microsoft.com/en-us/azure/ai-services/language-service/entity-linking/overview)。總覽的 supported legacy 描述不會取消這些公告；上述日期只在這裡集中保留，用於區分 Retiring 與 Retired。

| 對照項目 | 如何理解與作答 |
| --- | --- |
| **Current terminology** | Azure Language in Foundry Tools；NER、Language detection 為核心；上述特定功能為 Retiring |
| **Legacy / exam-bank terminology** | Cognitive Services / Text Analytics；LUIS / CLU 意圖辨識；unknown → NaN 舊題預期 |
| **What to answer if this wording appears on an older question** | 按題目要的輸出分辨 Key phrases、NER、Entity Linking 等；明確為原 unknown 題庫時記 C. NaN，但現行官方 API 範例為 0.0；若題目問目前新建方案，不能以舊題名詞取代目前部署指引 |

## 7. Exam Keywords & Triggers

| Question trigger | Think | 排除混淆 |
| --- | --- | --- |
| main concepts / important phrases | Key Phrase Extraction | 不需要生成段落 |
| people / places / organizations | NER | 若還要 Wikipedia link 才進一步考 linking |
| positive / negative / neutral | Sentiment Analysis | 非 language confidence |
| sentiment about the food / service | Opinion Mining | 把情緒連到對象 |
| shorten a long document | Summarization | 留意 extractive vs abstractive |
| ISO code / predominant language | Language Detection | 非 translation |
| user intention and details | CLU concepts | intent 是動作、entity 是參數 |

## 8. Memory Rules

- **Key phrases = What about?** 找主題；**NER = Who / Where / When?** 找實體。
- **Sentiment = feeling；Opinion mining = feeling about what.**
- **Extractive = select；Abstractive = rewrite.**
- **Utterance = words；Intent = goal；Entity = details.**
- **Language ≠ Script；Detection ≠ Translation.**
- **Exam priority ≠ lifecycle status.** 綱要仍測的 legacy-classified 功能仍要讀。

## 9. Official Sources

本次查核 **2026-09-06**。範例值與自訂情境不是實際服務執行結果。

| Source | 用途 |
| --- | --- |
| [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) | 考試技巧與實作範圍 |
| [Language overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/overview) | 正式名稱、core / legacy 分類、Entity linking |
| [Entity Linking overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/entity-linking/overview) | 消歧義與退役公告 |
| [Key phrase extraction](https://learn.microsoft.com/en-us/azure/ai-services/language-service/key-phrase-extraction/overview) | 主題片語 |
| [NER overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/named-entity-recognition/overview) | 實體辨識 |
| [Sentiment / opinion mining](https://learn.microsoft.com/en-us/azure/ai-services/language-service/sentiment-opinion-mining/overview) | 情緒與對象 |
| [Summarization overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/summarization/overview) | 摘要種類 |
| [Language detection behavior](https://learn.microsoft.com/en-us/azure/ai-services/language-service/language-detection/how-to/call-api) | Unknown、confidence、script、mixed language |
| [Analyze Text REST 2026-05-01](https://learn.microsoft.com/en-us/rest/api/language/analyze-text/analyze-text/analyze-text?view=rest-language-analyze-text-2026-05-01) | request / response 與版本欄位 |
| [CLU overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/conversational-language-understanding/overview) | 意圖與實體 |
| [Translator overview](https://learn.microsoft.com/en-us/azure/ai-services/translator/overview) | 文字／文件翻譯服務邊界 |
