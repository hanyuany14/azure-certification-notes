# Content Understanding 內容理解與結構化擷取

## 1. Module Overview

| Item | Details |
|---|---|
| Official service/module name | **Azure Content Understanding in Foundry Tools** |
| AI-901 relevance | **High**：從 documents、images、audio、video 擷取內容與結構化資訊；辨識 analyzer、schema 與輸出 |
| Current status | **Mixed**：服務與既有功能 Current；`2026-06-01-preview` 等新 API / 部分 SDK 是 Preview，依功能與版本區分 |
| Main exam workloads | Content extraction、field extraction、classification、document processing、audio/video analysis |
| Primary Microsoft products | Azure Content Understanding、Microsoft Foundry；比較 Azure Document Intelligence、Azure Vision、Azure Speech |

核對日期：**2026-09-10**。本模組對應現行考綱的 **documents/forms、images、audio/video extraction** 與 **lightweight client**。以 GA 核心概念與題目中的 analyzer 為主；新 Preview 的進階功能只在有名稱或參數差異時提示。[Current AI-901 scope](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)

## 2. Core Concepts

### 2.1 先分清楚：讀內容、分種類、抽欄位、輸出格式

| Official English Term | 中文解釋 | What it does | Input | Output | Exam Keywords |
|---|---|---|---|---|---|
| **Analyzer** | 定義這次分析怎麼做 | 組合內容處理設定、schema 與模型設定 | Content + configuration | Analysis result | prebuilt、custom、analyzer ID |
| **Content Extraction** | 把內容與基本結構讀出來 | 依 modality 擷取文字、版面、表格、逐字稿或鏡頭資訊 | Document / Image / Audio / Video | 基礎內容與結構 | text、layout、tables、transcript |
| **Field Extraction** | 按欄位規格填入答案 | 將資訊對應到明確的欄位名稱、型別、巢狀結構 | Content + field schema | Structured fields | named fields、schema、JSON |
| **Classification** | 判斷是哪一類內容 | 使用指定類別標記內容；也可作為欄位方法 | Content + categories | Class label | document type、categorize |
| **Markdown Output** | 內容的可閱讀表示 | 用標題、段落、表格或逐字稿等組織結果 | 已分析內容 | Markdown text | readable text、RAG、chunking |
| **Source / source locations** | 擷取結果的來源證據 | 指出文字或欄位由哪份輸入內容、哪個位置而來 | Analysis result details | 引用／位置資訊，形式依 API | grounding、where did it come from |
| **Field Schema / fieldSchema** | 告訴分析器想取得哪些資料 | 定義欄位、型別、描述與結構 | 欄位定義 | 可執行的擷取規格 | schema-defined fields |
| **Prebuilt Analyzer** | 已備妥的常見任務設定 | 減少自行定義 analyzer 的工作 | 對應內容 | 該 analyzer 的預設結果 | minimal configuration |
| **Custom Analyzer** | 配合業務調整規格 | 增加欄位、修改描述與分析設定 | 自訂 schema / config | 自訂結構化結果 | specific business fields |

**Markdown** 是輸出表示，**JSON** 是回應容器；看到 JSON 本身不能斷定在做 Field Extraction。考點是是否要求 `InvoiceNumber`、`TotalAmount`、`LineItems` 等**有業務意義的指定欄位**。[Analyzer reference](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/analyzer-reference)

### 2.1a 圖片索引：Markdown、Fields、Source 各做什麼

| 需求 | 選擇／設定 | 為何適合 |
|---|---|---|
| 將產品圖片的描述文字放進 search index | 將 **Markdown output** map 到可搜尋的 content field | Markdown 是可索引的描述內容，例如產品、顏色與用途的文字說明 |
| 取得固定的產品類別 | 在 field schema 定義欄位，使用 **`classify`** method 與類別集合 | 回傳可供下游程式使用的 structured category value |
| 找出擷取值在圖片中的來源位置 | 讀取 **source locations** | 用於 grounding／追溯，不是搜尋文件的描述內容 |
| 審核有害圖片 | Azure AI Content Safety | 內容安全風險分類是不同目的，不會自動產出可搜尋產品描述 |

例如一張跑鞋圖，Markdown 可供搜尋「black running shoe」；`ProductCategory` 這類欄位可回傳 `Shoes`；source locations 說明值來自輸入的哪個位置。三者可同時存在，但不要把 source location 當成 search index 的內容欄位。

### 2.2 四種輸入，對應不同基礎內容

| Modality | Base / common prebuilt analyzer | 核心輸出 | 適合的應用 |
|---|---|---|---|
| **Document** | `prebuilt-document`；`prebuilt-read` / `prebuilt-layout`；`prebuilt-invoice` | Text、layout、tables、invoice fields、line items | 文件 OCR、發票與合約自動化 |
| **Image** | `prebuilt-image`；`prebuilt-imageSearch` | 圖像描述、可用文字、圖表摘要或自訂 fields | 資產搜尋、圖像資訊整理 |
| **Audio** | `prebuilt-audio`；`prebuilt-audioSearch`；`prebuilt-callCenter` | Transcript、speaker、timestamps、summary 或 call-center fields | 通話後分析、Podcast 整理 |
| **Video** | `prebuilt-video`；`prebuilt-videoSearch` | Transcript、shots、key frames、segments 或自訂 fields | 影片索引、片段檢索、RAG |

> **Analyzer 階層：**`prebuilt-document`、`prebuilt-image`、`prebuilt-audio`、`prebuilt-video` 是建立 custom analyzer 時可繼承的四個 **base analyzers**；`*Search` 是針對 RAG／搜尋內容準備的 analyzer；invoice、call center 等名稱則代表特定情境。題目問「處理哪種 modality」與「要哪種業務輸出」時要一起判斷。

只有文字轉錄與播放需求時，先看 [Azure Speech](06_Speech.md)；要把文件或錄音按 schema 轉成業務資料時，才是本模組的重點。

### 2.3 Schema：欄位意義、型別與 relationships

| Schema 概念 | 中文理解 | 例子 | 容易混淆之處 |
|---|---|---|---|
| **Field name** | 程式取值的名稱 | `SupplierName` | 欄位名不必與文件印出的文字完全相同 |
| **description** | 說明想取什麼 | `Name of the supplier or vendor` | 可涵蓋同義詞，不只精確字串比對 |
| **string** | 文字 | Customer / product name | 數字外觀的編號也可能應是 string |
| **number** | 可含小數的數值 | `TotalAmount: 5000.5` | 型別不是把貨幣符號全部當字串 |
| **integer** | 整數 | `Quantity: 3` | 若數量可能為 1.5，應選 number |
| **boolean** | 是 / 否 | `IsPaid` | 不是任意描述文字 |
| **date** | 日期 | Invoice date | 格式依 API schema；不要與普通字串混用 |
| **object** | 一組彼此相關的欄位 | 一筆 item 的名稱、數量、價格 | 保留同一筆資料內的對應關係 |
| **array** | 重複項目的清單 | 多筆 line items | 每筆可是一個 object，不是把整張表壓成一段文字 |

型別表對應 **API `2025-11-01`**，其 REST reference 明列 `integer`；該版本另有 `time` 與 `json`。`date` 正規化為 `YYYY-MM-DD`；不要因某篇概念頁只列常見型別就推論其餘型別已移除。[Field types](https://learn.microsoft.com/en-us/rest/api/contentunderstanding/content-analyzers/get?view=rest-contentunderstanding-2025-11-01)

**Relationships** 在這裡主要是結構上的歸屬：哪個值屬於哪個欄位、哪個物件、哪一列。它不表示一定在建立 graph database 或訓練 entity-linking 模型。

例如文件讀到 `Product: Mouse / Qty: 3 / Price: 500`，希望保留的是一筆 `{ "Product": "Mouse", "Quantity": 3, "Price": 500 }`，不能只抽出三個失去對應關係的值。

## 3. How It Works

### 3.1 共通流程

```text
Document / Image / Audio / Video
  → 選擇 prebuilt analyzer，或定義 custom analyzer
  → Content extraction：取得基本內容與結構
  → Field extraction：依 schema 產生具名欄位
  → Analysis result：Markdown + structured fields + 可用的來源細節
  → 檢查結果，再交給搜尋、RAG 或業務程式
```

並非每次分析都必須定義自訂欄位；可依需要只擷取內容。回傳細節與功能由 **analyzer configuration / API version** 決定，不能假設每個結果都有相同欄位。

### 3.2 版面變動、手寫與語義欄位

| 文件 | 畫面上的字 | 實際要找的欄位 |
|---|---|---|
| Form A | `Supplier Name: Contoso`、`Total: $5,000` | `SupplierName`、`TotalAmount` |
| Form B | `Vendor: Contoso`、`Amount Due: $5,000` | 同一組欄位 |
| 手寫 Form C | `公司：Contoso`、`應付金額：5000` | 同一組欄位，但辨識品質需驗證 |

**Exact label matching** 可能不認得同義詞；**fixed page coordinates** 容易因版面位移失敗。用 schema 描述欄位意義更能表達需求；這是設計方式，不是任何手寫內容都能百分之百擷取成功的保證。

```json
{
  "fieldSchema": {
    "fields": {
      "SupplierName": {
        "type": "string",
        "description": "Name of the supplier or vendor"
      },
      "TotalAmount": {
        "type": "number",
        "description": "Total amount payable"
      },
      "Items": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "Product": {"type": "string"},
            "Quantity": {"type": "number"},
            "Price": {"type": "number"}
          }
        }
      }
    }
  }
}
```

欄位也可指定 **method**：`extract` 取來源值、`classify` 選預定義類別、`generate` 產生摘要等內容；不同 modality / API 的限制須依版本核對。

這是 analyzer 的 **fieldSchema 片段**，不是完整 create-analyzer request。正式設定還需 base analyzer、必要模型與其他配置。[Analyzer reference](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/analyzer-reference)

應用程式整理後可以得到以下容易使用的資料形狀；**不是聲稱服務原始 response 就長這樣**：

```json
{
  "customerName": "Contoso",
  "date": "2026-09-06",
  "total": 1500,
  "items": [{"name": "Mouse", "quantity": 3, "price": 500}]
}
```

服務的 REST fields 可包含 `type`、`valueString`、`valueNumber`、`valueArray`、`valueObject` 等型別化資訊；SDK 可能透過 `.value` 存取。讀取資料時必須依實際結果結構處理，不能把上述示意結果當成通用 response parser。

### 3.3 Audio：從逐字稿到通話洞察

```text
客服錄音
  → Speech recognition：聲音轉文字
  → Speaker diarization：區分 Speaker 1 / Speaker 2
  → Role detection（依 analyzer）：判斷 Agent / Customer
  → Summary / Sentiment / Topics / People / Companies 等欄位
```

| 能力 | 中文理解 | 輸出例子 |
|---|---|---|
| **Transcription** | 說了什麼 | 逐字稿；可用 WebVTT 表示 |
| **Speaker Diarization** | 哪段是哪位說話者 | Speaker 1、Speaker 2；不等於知道真實身分 |
| **Speaker Role Detection** | 判斷對話角色 | Agent / Customer |
| **Timestamp** | 何時說這句 | 詞句的 start / end；依設定回傳 |
| **Field Extraction** | 依需求整理通話資訊 | 摘要、情緒、主題、提及人物/公司 |

例如客服說「您好，請問需要什麼協助？」、客戶說「我的信用卡刷不過」，可以先得到帶 speaker 的 transcript，再整理問題摘要。Role 與 sentiment 都是分析結果，可能判錯，不能當成已驗證的事實。[Audio overview](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/audio/overview)

### 3.4 Video：同一份結果有聲音與畫面時間軸

```text
Video
  ├─ Audio → Recognition + Diarization → Transcript phrases
  └─ Visual → Shot detection + Key-frame extraction → 畫面時間點
       → 結合影音內容 → 自訂 fields / scene segments
```

| REST / JavaScript Property | 中文含義 | 典型內容 | 重要條件 |
|---|---|---|---|
| **`transcriptPhrases`** | 誰在何時說什麼 | `speaker`, `text`, `startTimeMs`, `endTimeMs` | 詳細結果依 `returnDetails` |
| **`keyFrameTimesMs`** | 代表畫面的時間點 | 毫秒 timestamp array | 不是 image bytes；取實際 frame 需對應影像資產 |
| **`cameraShotTimesMs`** | 切鏡頭時間點 | Shot changes 的毫秒 array | 詳細結果依 `returnDetails` |
| **`startTimeMs` / `endTimeMs`** | 該段 content 的起訖 | Segment / content 的時間範圍 | 不必然代表整部影片 |
| **`width` / `height`** | 影像畫素尺寸 | Frame resolution | 純音訊無此影像尺寸意義 |
| **`fields`** | 業務結構化資訊 | 摘要、品牌、主題等 schema fields | 欄位取決於 analyzer |
| **`markdown`** | 可閱讀內容 | Transcript、key-frame 引用、描述 | 用於檢視、搜尋、RAG |
| **`segments`** | 分析出的內容片段 | 自訂 segmentation 的子片段 | 設定/API 版本相關 |

**Key Frame** 是代表畫面，**Camera Shot** 是鏡頭變化，兩者不是同一時間軸。原筆記「每個短 shot 至少採樣一個 frame」保留其用意為充分代表鏡頭；目前官方頁僅說明會擷取足以代表各 shot 的 key frames；考試不需要背尚未核實的最低採樣保證。[Video overview](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/video/overview)

## 4. Important API / SDK Patterns

### 4.1 選對 analyzer 比自己重建全部設定更重要

| Analyzer | 核心輸出 | Speaker 結果 | Summary / Sentiment | 情境 |
|---|---|---|---|---|
| **`prebuilt-callCenter`** | 通話後分析與結構化資訊 | **Agent / Customer** 角色 | 預建摘要與情緒 | 客服錄音，要求 minimal configuration |
| **`prebuilt-audioSearch`** | 一般對話逐字稿與摘要 | Speaker 1 / Speaker 2 | 有摘要；不把 call-center sentiment 當通用預設 | Podcast、meeting、音訊搜尋/RAG |
| **Customized prebuilt analyzer** | 加入自訂欄位或調整設定 | 依配置 | 依 schema | 在既有能力上增加公司需求 |
| **Custom audio analyzer** | 自訂 field schema 與分析設定 | 依配置 | 依 schema | 與通用/客服模板不同的企業需求 |
| **`prebuilt-invoice`** | 發票具名欄位與明細 | 不適用 | 不適用 | 快速開始文件欄位擷取 |
| **`prebuilt-imageSearch`** | 圖像描述等可搜尋資訊 | 不適用 | 依 analyzer | 圖像內容索引 |
| **`prebuilt-videoSearch`** | 逐字稿、key frames、片段等 | 依影音內容 | 依 analyzer | 現行 prebuilt catalog 與 quickstart 明列的影片 RAG analyzer |

這些是 **同一服務內的 analyzer 選擇**。`prebuilt-callCenter` 也能提供 Topics、Companies、People、Categories 等欄位；確切輸出以該版本 analyzer schema 為準。[Audio analyzers](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/audio/overview)、[Quickstart](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/quickstart/use-rest-api)

客製化可以先 GET 預建 analyzer 定義、修改 schema/config，再建立自己的 analyzer；**不是把任何 `prebuilt-*` 名稱都直接填入 `baseAnalyzerId`**。現行文件列出的可繼承 base 僅 `prebuilt-document`、`prebuilt-image`、`prebuilt-audio`、`prebuilt-video`。[Prebuilt analyzers](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/prebuilt-analyzers)

### 4.2 Python SDK：提交、等待、讀取

以下依官方 quickstart 的 **Preview SDK** 模式縮成考試可辨識的範例。需先設定 Foundry resource、可用的模型部署 defaults、endpoint、credential 及可存取的文件 URL；沒有實際呼叫服務。

```python
import os
from azure.ai.contentunderstanding import ContentUnderstandingClient
from azure.ai.contentunderstanding.models import AnalysisInput
from azure.core.credentials import AzureKeyCredential

client = ContentUnderstandingClient(
    endpoint=os.environ["CONTENTUNDERSTANDING_ENDPOINT"],
    credential=AzureKeyCredential(os.environ["CONTENTUNDERSTANDING_KEY"]),
)
poller = client.begin_analyze(
    analyzer_id="prebuilt-invoice",
    inputs=[AnalysisInput(url=os.environ["INVOICE_URL"])],
)
result = poller.result()
for content in result.contents or []:
    print(content.markdown)
    for name, field in (content.fields or {}).items():
        print(name, field.value)
```

| 元件 / 參數 | 意義 | 考點 |
|---|---|---|
| **`ContentUnderstandingClient`** | 與服務互動的 client | 不是 `SpeechRecognizer` 或 `AIProjectClient` |
| `endpoint` / `credential` | 服務位置與驗證 | 不把金鑰硬寫進程式；亦可依官方說明使用 Entra ID |
| **`analyzer_id`** | 使用哪份分析設定 | 與 model deployment name 不同 |
| **`AnalysisInput(url=...)`** | 要分析的來源 | 這段使用 URL input；不是傳 portal 頁面 |
| **`begin_analyze`** | 啟動長時間分析 | 回傳 poller，不是立即取得最終所有內容 |
| **`poller.result()`** | 等待完成並取結果 | 處理成功/失敗後再讀內容 |
| **`result.contents`** | 一個或多個內容區段 | 不應假設永遠只有一段 |
| **`fields` / `.value`** | 型別化欄位與值 | Array / object 可能需逐層展開 |

官方 quickstart 在核對時使用 `pip install --pre azure-ai-contentunderstanding`；這是 **SDK Preview** 訊號，不能推論整個服務都是 Preview。[Quickstart](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/quickstart/use-rest-api)

### 4.3 影音細節與命名差異

| REST / JavaScript | Python SDK | 使用時機 |
|---|---|---|
| `transcriptPhrases` | `transcript_phrases` | 逐句讀取文字與說話者 |
| `startTimeMs` | `start_time_ms` | 時間起點 |
| `endTimeMs` | `end_time_ms` | 時間終點 |
| `keyFrameTimesMs` | `key_frame_times_ms` | 代表畫面時間點；已依 Python model reference 核實 |
| `cameraShotTimesMs` | `camera_shot_times_ms` | 切鏡頭時間點 |

[Python AudioVisualContent reference](https://learn.microsoft.com/en-us/python/api/azure-ai-contentunderstanding/azure.ai.contentunderstanding.models.audiovisualcontent?view=azure-python-preview) 核實上表 snake_case；原題 camelCase 描述 JSON elements，不表示 Python object attribute 也照抄。

`returnDetails` 是 analyzer configuration 控制，不是所有 SDK 呼叫都可以任意加的同名頂層參數。請分清 **配置 analyzer**、**呼叫 analyze**、**讀取 result** 三個階段。

## 5. Comparison / Common Confusions

| Concept A | Concept B | Key Difference |
|---|---|---|
| **Content Extraction** | **Field Extraction** | 讀出一般內容與結構 vs 按指定業務 schema 取值 |
| **Classification** | **Field Extraction** | 判斷 Invoice / Contract / Resume vs 取得 invoice number / total |
| **Markdown Output** | **Field Extraction** | 可閱讀格式 vs 明確型別、具名欄位與 repeated records |
| **OCR** | **Schema-based extraction** | 讀出字 vs 理解該值屬於什麼欄位 |
| **Exact label matching** | **Semantic field description** | 固定文字標籤 vs 描述 Supplier / Vendor 等共同意義 |
| **Fixed coordinates** | **Field schema** | 固定位置 vs 目標欄位與結構；來源座標仍可用來追溯結果 |
| **Source normalization** | **Field schema** | 輸入前處理 vs 定義想取得的資料；兩者可同時使用 |
| **Object** | **Array of objects** | 一筆有關聯的欄位 vs 多筆同型資料 |
| **Speaker diarization** | **Role detection** | Speaker 1 / 2 vs Agent / Customer |
| **Transcript timestamps** | **Key-frame timestamps** | 聲音語句時間 vs 視覺代表畫面時間 |
| **Shot** | **Semantic segment** | 鏡頭剪接邊界 vs 依主題/需求形成的片段 |

### OCR、Document Intelligence、Content Understanding 怎麼選

| 需求 / 服務 | 輸入與輸出重點 | 選擇理由 |
|---|---|---|
| **OCR / Read** | 影像文字 → recognized text | 只需要把印刷/手寫字讀出來 |
| **Azure Document Intelligence** | 文件 → text、layout、tables、key-value pairs 或 prebuilt/custom model fields | 題目明確指定該服務或文件模型流程 |
| **Azure Content Understanding** | Document / Image / Audio / Video → content + schema-defined fields | 多模態內容或題目指定 analyzer / fieldSchema |
| **Azure Speech** | 語音 ↔ 文字；語音相關功能 | 專門 recognition / synthesis；見 [Speech](06_Speech.md) |
| **Vision-capable generative model** | 圖片 + prompt → 回答 | 互動式看圖問答；見 [Computer Vision](07_Computer_Vision.md) |

**原筆記更正：**「表格 / key-value pairs → 一定選 Document Intelligence」忽略了 Content Understanding。表格是輸出需求，還需看指定服務與 schema；OCR 也可能是文件處理流程的一部分，不能說文件理解完全不用 OCR。[Document Intelligence overview](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/overview)、[Content Understanding overview](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/overview)

## 6. Current vs Legacy

| 名稱 / 說法 | 狀態與核對結果（2026-09-06） | 維護筆記時怎麼處理 |
|---|---|---|
| **Azure AI Content Understanding → Azure Content Understanding in Foundry Tools** | 舊/新品牌語境；概念 Current | 原名稱保留供辨識；主要使用現行官方名稱 |
| **API `2025-11-01`** | **Current / GA**；官方建議用於 production，支援四種 modality 的核心擷取 | 保留已驗證 analyzer / response 模式，不混入新 Preview 的專屬配置 |
| **API `2026-06-01-preview`** | **Preview**；目前 video / analyzer 文件含此版本 | 先檢查該功能所屬版本，不能自動視為前版 schema |
| **Python Content Understanding SDK** | Quickstart 使用預發行套件 | 記住 client / poller / result；實作前固定使用的套件版本 |
| **`prebuilt-videoSearch` vs `prebuilt-videoAnalysis`** | 現行 catalog / quickstart 核實 `videoSearch`；video overview 另出現 `videoAnalysis` | 教學與題目保留 **videoSearch**；**NEEDS VERIFICATION** 僅限 `videoAnalysis` 的精確版本/可用性，不能推論 videoSearch 已被取代 |
| **JSON 就是 Field Extraction** | 原口訣過於簡化 | 改成「具名 fields + schema + repeated rows」才強烈指向 Field Extraction |
| **Coordinates 沒用** | 原筆記把選項排除說成絕對規則 | 固定座標不是 schema 定義；但位置可提供 grounding / source evidence |
| **每個結果必有影音詳細欄位** | 原筆記漏掉配置條件 | 細節取決於 `returnDetails`、modality、API 與 analyzer |
| **每個短 shot 至少一張 key frame** | 原始說法未獲現行文件精確保證 | 不列為應背規則；原文仍保存在 source，題目應辨認代表畫面與鏡頭切換 |

本模組的文件/影像/音訊/影片處理核心不是 legacy；**Preview**、舊名稱、舊題庫預設答案是不同維度。若舊題考到已變更的預設值，後續 mistakes 應同時列原 expected answer 與現行證據。

## 7. Exam Keywords & Triggers

| If the question says... | Think... |
|---|---|
| document type / categorize | **Classification** |
| text / layout / document structure | **Content Extraction** |
| readable structured text / RAG / chunking | **Markdown Output** |
| descriptive image text for search index | 將 **Markdown Output** map 到 content field |
| fixed category value from an image | schema field with **`classify`** |
| evidence / where an extracted value came from | **source locations**，不是 search content |
| named fields / schema / invoice number / vendor / total | **Field Extraction** |
| repeated rows / line items | **Array of Objects** |
| handwritten / visually complex / varying layout | **Schema-defined fields and relationships** |
| same information with different labels | 描述欄位意義，不只 exact label matching |
| Agent / Customer + summary + sentiment + minimal configuration | **`prebuilt-callCenter`** |
| general conversation / searchable audio / summary | **`prebuilt-audioSearch`** |
| who spoke / phrase start and end | **`transcriptPhrases`** |
| representative frames / key-frame timestamps | **`keyFrameTimesMs`** |
| cuts / camera transitions | **`cameraShotTimesMs`** |
| video resolution | **`width` / `height`** |
| custom structured values | **`fields`** |
| asynchronous analyzer operation | **submit → poll → result** |

## 8. Memory Rules

- **Content 讀內容；Field 填欄位；Classification 分類；Markdown 呈現。**
- **Markdown 可搜尋；Field 可自動化；Source 可追溯。**
- **Schema 寫意義，Object 保關係，Array 留多筆。**
- **Diarization 分誰說；Role detection 分扮演什麼角色。**
- **Transcript 是聲音時間軸；Key frame 是代表畫面；Shot 是切鏡頭。**
- **Analyzer 決定抽什麼；API / SDK version 決定怎麼呼叫、怎麼讀。**

## 9. Official Sources

核對日期：**2026-09-10**。下列以 English Microsoft Learn 為主；頁面中的 Preview 提示與舊示例需一起讀。

- [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)：內容擷取與多模態範圍。
- [Content Understanding overview](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/overview)：服務與處理輸入。
- [Image overview](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/image/overview)：圖片 Markdown、欄位與索引情境。
- [Analyzer configuration and reference](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/analyzer-reference)：analyzer、fieldSchema、config、nested fields。
- [Best practices](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/best-practices)：欄位描述與複雜結構設計。
- [Audio overview](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/audio/overview)：transcription、diarization、role detection、callCenter / audioSearch。
- [Video overview](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/video/overview)：transcript、shots、key frames、segments 與 Preview 差異。
- [Quickstart: REST API and SDKs](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/quickstart/use-rest-api)：Python client、AnalysisInput、begin_analyze、poller、typed fields。
- [Prebuilt analyzer catalog](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/prebuilt-analyzers)：analyzer ID、base analyzer 與客製方式。
- [Python AudioVisualContent](https://learn.microsoft.com/en-us/python/api/azure-ai-contentunderstanding/azure.ai.contentunderstanding.models.audiovisualcontent?view=azure-python-preview)：snake_case 欄位。
- [AudioVisualContent interface](https://learn.microsoft.com/en-us/javascript/api/@azure/ai-content-understanding/audiovisualcontent?view=azure-node-latest)：影音詳細欄位與配置條件。
- [Content Analyzers: Get, API 2025-11-01](https://learn.microsoft.com/en-us/rest/api/contentunderstanding/content-analyzers/get?view=rest-contentunderstanding-2025-11-01)：舊題對應的 schema 型別。
- [Document Intelligence overview](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/overview)：文件辨識服務邊界。
