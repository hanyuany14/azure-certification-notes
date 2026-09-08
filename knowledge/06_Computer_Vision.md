# Computer Vision 電腦視覺

## 1. Module Overview

| Item | Details |
|---|---|
| Official service/module name | **Computer Vision**；**Azure Vision in Foundry Tools**；**Azure Face**；Microsoft Foundry 中的視覺與影像生成模型 |
| AI-901 relevance | **High**：辨識視覺工作負載、使用多模態影像輸入與影像生成；舊版 taxonomy、Face 細節屬 **Medium / Legacy exam-bank context** |
| Current status | **Mixed**：任務概念仍 Current；Image Analysis 4.0 為 Deprecated / Retiring；各服務與 API 版本另見第 6 節 |
| Main exam workloads | Image classification、object detection、OCR、image analysis、multimodal image interpretation、image generation |
| Primary Microsoft products | Azure Vision、Azure Face、Azure OpenAI in Microsoft Foundry Models、Azure Custom Vision；文件結構擷取另見 [Content Understanding](07_Content_Understanding.md) |

核對日期：**2026-09-06**。優先順序：**多模態圖片輸入、影像生成與輕量 client → 視覺任務概念 → 舊版服務辨識**。Semantic / Instance Segmentation 是輔助概念；Face 操作與第 6 節舊版 catalog 用於辨識既有題目，不擴張成現行考綱重心。[Current AI-901 scope](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)

## 2. Core Concepts

### 2.1 任務、服務與輸出層級

| Official English Term | 中文解釋 | What it does | Input | Output | Exam Keywords |
|---|---|---|---|---|---|
| **Computer Vision** | 讓系統解讀影像的領域 | 涵蓋分類、偵測、讀字等不同任務 | Image / Video | 依任務而定 | visual information、images |
| **Image Analysis** | Azure Vision 的影像分析功能群 | 呼叫指定 visual features | Image | Tags、captions、objects 等 | analyze image、visual features |
| **Image Classification** | 判斷整張圖屬於什麼 | 對影像給分類結果 | Image | Label / scores | classify an image |
| **Object Detection** | 辨認每個物體並定位 | 找類別與大致範圍 | Image | Class + **Bounding Box** + confidence | locate、bounding box |
| **Semantic Segmentation** | 每個像素分到一種類別 | 同類物體使用相同 class | Image | 每個 **Pixel** 的 class | pixel-level classes |
| **Instance Segmentation** | 同類也分出不同個體 | 各物件各有輪廓 | Image | 每個 instance 的 **Mask** | individual objects、pixel masks |
| **Optical Character Recognition (OCR) / Read** | 讀出圖片中的字 | 辨認印刷或手寫文字 | 文字照片、掃描文件 | Text、位置等，依 API 而定 | printed / handwritten text |
| **Image Tagging / Tags** | 用多個詞描述畫面 | 描述物體、生物、場景、動作 | Image | Tags + confidence | descriptive metadata、attributes |
| **Image Captioning / Captions** | 用自然語言描述畫面 | 產生完整描述句 | Image | Caption + confidence | describe an image、sentence |
| **People Detection** | 找出圖片中的人 | 回傳人物所在位置 | Image | Person bounding boxes | people、location |
| **Multimodal Vision** | 模型同時理解文字與圖像 | 按問題解讀、比較圖片 | Text + one/more images | 文字回答等 | compare images、visual question answering |
| **Image Generation** | 依描述建立新圖片 | 將 prompt 轉成影像；支援能力依模型 | Text，編輯時可含 image | 新 image | generate、create、edit image |

**Computer Vision** 是領域，**Azure Vision / Image Analysis** 是服務與功能群，**Classification / Detection / OCR** 是任務。不要把三種層級當成互斥答案。

### 2.2 Multiclass、Multilabel、Detection 與 Segmentation

| 類型 | 判斷單位 | 可同時有多個 label？ | 區分同類不同個體？ | 位置輸出 | 例子 |
|---|---|---|---|---|---|
| **Multiclass Image Classification** | 整張圖片 | 最終通常選一個互斥 class | 否 | 無 | Cat / Dog / Bird 三選一 → Dog |
| **Multilabel Image Classification** | 整張圖片 | 可以；各 label 分別判斷 | 否 | 無 | Dog + Beach + Outdoor |
| **Object Detection** | 每個物體 | 可含多種類別 | 是 | Bounding Box | Dog #1、Dog #2、Cat 各一個框 |
| **Semantic Segmentation** | Pixel | 每個像素有其 class | 同類不分 instance | 像素 class map | 兩隻貓都標成 Cat |
| **Instance Segmentation** | 每個物件的 Pixel | 可含多種類別 | 是 | 每個 instance 的 mask | Cat #1 與 Cat #2 各有輪廓 |

分類模型可以回傳多個候選分數；**Multiclass** 的核心是目標類別互斥，不能把「回傳多個分數」直接當成 **Multilabel**。Detection 可以據偵測結果計數，但有漏偵測時不保證得到真實總數。

![原始視覺任務投影片：分類、偵測與分割](../assets/knowledge/06_vision_tasks.png)

**原圖更正：** 投影片將輸出越細緻描述成 **Accuracy** 越高，容易誤導。Classification、Detection、Segmentation 解的是不同任務，不能直接排成準確率高低。應比較 **Label → Bounding Box → Pixel Class / Instance Mask** 的輸出粒度。來源：Notion 匯出 `AI-901 Notes/image 2.png`，原圖保留。概念核對：[Microsoft segmentation lesson](https://github.com/microsoft/AI-For-Beginners/blob/main/lessons/4-ComputerVision/12-Segmentation/README.md)。

### 2.3 Azure Face：偵測不等於身分辨識

| Official English Term | 中文解釋 / 用途 | Input | Output / 比對方式 | 例子與限制 |
|---|---|---|---|---|
| **Face Detection** | 找出人臉位置 | Image | Face rectangle；依選項回傳 landmarks / attributes | 合照框出三張臉，不知道姓名 |
| **Face Attributes** | 描述可用的臉部特徵 | 偵測到的臉 | Head pose 等；依 detection model 和存取權限 | 不能假設舊版所有屬性仍可用 |
| **Face Identification / Identify** | 從已知人物集合找出可能身分 | 一張臉 + 已註冊人物集合 | **1-to-many**；候選人物與 confidence | 從員工資料庫找可能是誰 |
| **Face Verification / Verify** | 檢查是否為同一人 | 兩張臉，或臉與已註冊 person | **1-to-1**；相同身分判斷與 confidence | 自拍與證件照片比對 |
| **Find Similar Faces** | 從候選臉搜尋匹配 | Target face + candidate faces | 相似人臉清單 | **matchPerson** 著重同一人；**matchFace** 著重外觀相似 |
| **Face Group / Group** | 將候選臉分群 | 多個 face IDs | 依相似性形成的 groups；可能含未歸組者 | 相簿人物整理；不等同回傳真實姓名 |
| **Custom Vision** | 訓練自訂分類 / detection | 標註過的自訂圖片 | 自訂模型預測 | 產品瑕疵、車種；不是身分驗證服務 |

**原筆記更正：**「Find Similar 只找長得像，不代表同一人」只適合概括 **matchFace**。官方另有 **matchPerson** 模式，需依題目模式判讀。Face overview 現行入口標示服務有申請資格與用途限制，僅適用 Microsoft managed customers / partners；identification / verification 也不可假設任何 Azure 帳戶都能直接使用。[Face overview](https://learn.microsoft.com/en-us/azure/ai-services/face/overview-identity)

## 3. How It Works

### 3.1 選擇視覺工作負載

| 需求 | 處理流程 | 檢查結果 |
|---|---|---|
| 圖片分類 | Image → trained classifier → class scores | 標籤是否互斥；選 multiclass 或 multilabel |
| 找物件位置 | Image → detector → objects + boxes | 每個 box 對應哪個 class、confidence |
| 擷取文字 | Image → OCR / Read → recognized text | 有無漏字；是否還需要欄位意義與表格結構 |
| 回答圖片問題 | Prompt + image content parts → vision-capable model | 回答是否符合畫面；小字、計數、定位仍可能出錯 |
| 建立新影像 | Description → image-generation model → image | 主體、構圖、風格是否符合要求 |

### 3.2 用同一張圖片理解不同輸出

場景：一隻狗在草地跑，背景有房子。

| 功能 | 可能輸出 | 解讀 |
|---|---|---|
| **Tags** | `dog`, `animal`, `grass`, `outdoor`, `building`, `running` + confidence | 多個描述詞，沒有固定繼承階層 |
| **Categories** | `animal_dog` | 舊版預定義 taxonomy 類別 |
| **Caption** | `A dog running on grass near a house.` | 完整語句，例子不是保證輸出 |
| **Objects** | `dog` + `(x, y, width, height)` | 找物體並框出位置 |
| **Image Type** | Clip art / line drawing 判定 | 圖片呈現形式，不是在問畫了什麼 |

### 3.3 Image Generation：補足現行綱要

Prompt 描述 **subject（主體）→ action / scene（動作與場景）→ composition（構圖）→ style（風格）→ constraints（限制）**。例如：`Create a clean product illustration of a reusable bottle on a white background, centered, with no text.`

| 操作 | 輸入重點 | 輸出與注意事項 |
|---|---|---|
| **Generate** | 描述希望建立的圖片 | 新影像；支援尺寸、品質等選項依模型 |
| **Edit** | 原圖 + 修改指示；部分 API 支援 mask | 修改後影像；需用支援 editing 的模型/API |
| **Image understanding** | 原圖 + 問題 | 描述/分析；不因能看圖就一定能生成圖 |

模型部署、prompt 設計與驗證流程見 [Microsoft Foundry](03_Microsoft_Foundry.md)。生成影像仍需檢查品質與安全性；不要把生成圖片當成真實事件的證據。[Microsoft image generation guide](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/dall-e)

## 4. Important API / SDK Patterns

### 4.1 Responses API：比較兩張圖片

下例為 Azure OpenAI **v1 Responses API** 結構示範；需提供真實 endpoint、金鑰、支援 vision 的 deployment 與可讀取的圖片網址。未呼叫付費服務實測。

```python
import os
from openai import OpenAI

client = OpenAI(
    base_url=os.environ["AZURE_OPENAI_BASE_URL"],
    api_key=os.environ["AZURE_OPENAI_API_KEY"],
)
response = client.responses.create(
    model=os.environ["AZURE_OPENAI_VISION_DEPLOYMENT"],
    input=[{
        "role": "user",
        "content": [
            {"type": "input_text", "text": "Compare these two images."},
            {"type": "input_image", "image_url": os.environ["IMAGE_URL_1"]},
            {"type": "input_image", "image_url": os.environ["IMAGE_URL_2"]},
        ],
    }],
)
print(response.output_text)
```

| 欄位 | 含義 | 易錯點 |
|---|---|---|
| `base_url` | Azure v1 endpoint，例如 `https://<resource>.openai.azure.com/openai/v1/` | 不要填 Foundry portal 網頁 URL |
| `model` | 已部署模型的 deployment name | 不是 agent name，也不必等於基礎模型名稱 |
| `input` | Responses API 的輸入 | 不直接套用 Chat Completions 的 `messages` 結構 |
| `type: input_text` + `text` | 文字指令 | 告訴模型要比較什麼 |
| `type: input_image` + `image_url` | 圖片輸入 | 一張圖一個 content part；URL 必須是字串 |
| `detail` | 圖片處理細節控制，依模型支援 | 較高細節可能增加 tokens / 延遲 |
| `output_text` | 彙整後的文字輸出 | 看圖回答不等同回傳新影像 |

**原始程式修正：** Python fence 原標成 `jsx`，已改為 `python`；原範例把 Markdown 連結 `[https://…](https://…)` 塞進 `image_url`，這不是正確 URL 字串。

### 4.2 本機圖片：Base64 Data URI

```python
import base64
from pathlib import Path

encoded = base64.b64encode(Path("photo.jpg").read_bytes()).decode("utf-8")
image_part = {
    "type": "input_image",
    "image_url": f"data:image/jpeg;base64,{encoded}",
}
```

遠端圖片使用 URL；本機圖片可以先轉 **Base64 Data URI**，同樣放進 `image_url`。MIME type 必須符合實際格式；PNG 使用 `image/png`。原筆記「兩種都放 `input_image`」成立於這種 URL/Data URI 輸入模式；API 也可能支援 `file_id`，不要理解成全服務只有一種傳圖方式。[Responses API](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/responses)

### 4.3 Image Analysis 與 Image Generation 的 API 概念

| Pattern | 關鍵概念 | 考試判讀 |
|---|---|---|
| **Image Analysis** | Image + requested visual features → structured analysis | 同一功能群可要求 tags、objects 等；依版本支援 |
| **Read / OCR** | 提交內容 → 同步結果或非同步 operation | 3.2 Read 與 4.0 同步 Read 不可混用流程 |
| **Images API** | `client.images.generate(model=deployment, prompt=...)` | 產生圖片，回傳表示方式依模型/API |
| **Responses image-generation tool** | `tools=[{"type": "image_generation"}]`，搭配相容 orchestration model 與影像部署 | 官方 Python 路徑仍使用 Preview header；從 `image_generation_call.result` 取影像，不從 `output_text` 取 |

這些是辨認 API 用途的短模式，不是可互換的完整呼叫。實際 model、endpoint、input/output format 必須使用同一份現行文件。[Image Analysis overview](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/overview-image-analysis)、[Image generation](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/dall-e)

### 4.4 Image Generation 的參數與輸出

以下是官方 **GPT-image** Images API 範例的核心呼叫片段；`image_client` 必須先依同一份文件建立完成。`model` 是影像部署名稱，不能直接沿用看圖用的聊天模型。

```python
result = image_client.images.generate(
    model=image_deployment,
    prompt="A reusable bottle, centered on a white background, no text.",
    n=1,
    size="1024x1024",
    quality="high",
    output_format="png",
)
image_bytes = base64.b64decode(result.data[0].b64_json)
```

| Parameter / output | 中文理解 | 不要混淆 |
|---|---|---|
| `prompt` | 要建立什麼畫面 | 不是遠端圖片 URL |
| `n` | 要產生幾張 | 上限依模型/API |
| `size` | 輸出影像尺寸 | 不是分析圖片的 `detail` |
| `quality` | 生成品質選項 | 不是辨識信心分數 |
| `output_format` | 圖片編碼格式 | 不是 `response_format`；支援選項依模型 |
| `data[0].b64_json` | 第一張影像的 Base64 資料 | GPT-image 範例先 decode 再寫成圖片；不是一般文字回答 |

片段需先 `import base64`，僅呈現題目有用的呼叫；不宣稱所有影像模型都支援完全相同參數。[Microsoft Images API example](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/dall-e)

## 5. Comparison / Common Confusions

| Concept A | Concept B | Key Difference |
|---|---|---|
| **Tagging** | **Categorizing** | 多個描述詞 vs 固定 taxonomy 的分類；Tags 沒有 category inheritance hierarchy |
| **Captioning** | **Tagging** | 完整句子 vs 多個關鍵詞 |
| **Image Type** | **Image Classification** | Clip art / line drawing 等形式 vs 圖中內容類別 |
| **Multilabel** | **Object Detection** | 多個標籤不附每個 instance 的位置；Detection 有 boxes |
| **Semantic Segmentation** | **Instance Segmentation** | 像素類別 vs 同類個體也分開 |
| **People Detection** | **Face Identification** | 人在哪裡 vs 是哪位已註冊人物 |
| **Face Detection** | **Face Verification** | 找到臉 vs 比對是否同一人 |
| **Domain-specific Model** | **Category / Tag** | 舊版特定領域辨識器 vs 分類/描述結果 |
| **OCR** | **Structured Field Extraction** | 讀到文字 vs 給文字欄位意義、型別與結構 |
| **Content organization** | **Tagging** | 相簿分類等應用行為 vs 產生影像描述 metadata 的功能 |
| **Vision-capable model** | **Image-generation model** | 解讀已有圖片 vs 建立新圖片；先檢查模型能力 |

**原筆記更正：**「看到表格或 key-value pairs 就一定是 Document Intelligence」過度簡化。**Azure Document Intelligence** 與 **Azure Content Understanding** 都能處理文件結構；依題目指定服務、prebuilt model、schema 與多模態需求選擇。完整對照保留在 [Content Understanding 第 5 節](07_Content_Understanding.md#5-comparison--common-confusions)。

## 6. Current vs Legacy

### 6.1 生命週期與命名

| 名稱 / 功能 | 狀態（核對 2026-09-06） | 舊題判讀與現在做法 |
|---|---|---|
| **Computer Vision / Azure AI Vision / Azure Vision in Foundry Tools** | **Mixed**：舊名稱與現行品牌並存文件路徑 | 看 API 版本與功能，不只看服務名稱；URL 仍可能有 `computer-vision` |
| **Image Analysis 4.0** | **Deprecated / Retiring**，2028-09-25 退休 | 保留 tags、caption、objects、Read 概念；新實作需閱讀 migration guidance |
| **Image Analysis 3.2 feature catalog** | **Legacy exam-bank context / Retiring** | 官方 migration guide 將 Image Analysis v3.2 與 v4.0 都列入 2028-09-25 退休範圍；不擴大推論到整個 Face 或所有 OCR 服務 |
| **Computer Vision 1.0 / 2.0 / 3.0 / 3.1** | **Retiring**，2026-09-13 | 本次核對日期尚未到退休日；不要提前寫成 Retired |
| **Azure Custom Vision** | **Retiring**，2028-09-25 退休 | 自訂分類/偵測概念保留；不當作 Face 身分驗證替代品 |
| **OCR** | 工作負載概念 **Current** | 某個舊 OCR endpoint 被歸類 legacy，不等於 OCR 技術本身 retired |
| **Azure Face** | **Current**，部分能力 Limited access | **emotion / gender 已 Retired**；age、smile、facial hair、hair、makeup 受限；不要沿用舊清單 |
| **Spatial Analysis** | **Legacy exam-bank context**；原筆記記載已退休，精確 lifecycle **NEEDS VERIFICATION** | 舊題的人流、越線、區域停留概念保留；原產品頁已轉向 Content Understanding Video，不能由重新導向推論替代服務完全相同；不背未充分核實的日期 |

來源：[Image Analysis migration guide](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/migration-options)、[Image Analysis](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/overview-image-analysis)、[Vision release history](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/whats-new)、[Custom Vision migration](https://learn.microsoft.com/en-us/azure/ai-services/custom-vision-service/migration-options)。

### 6.2 3.2 與 4.0 功能表：避免把舊功能全部搬進新 API

| 功能 | 3.2 catalog | 4.0 catalog | 有用的輸出 / 判讀 |
|---|---|---|---|
| **Tags** | 有 | 有 | `dog`, `grass` 等與 confidence |
| **Objects** | 有 | 有 | Class + bounding box |
| **Description / Caption** | Descriptions | Captions、Dense captions | 整圖描述；dense captions 可含區域描述 |
| **Categories / Taxonomy** | 有 | 不列為相同 feature | 固定的 86-category hierarchy |
| **Brands** | 有 | 不列為相同 feature | 品牌 / logo 與位置 |
| **Faces** | 舊版功能 | 不等同 4.0 People | Face rectangle；舊屬性須逐項核對 |
| **People** | 不列為相同 feature | 有 | 人物 boxes，不是姓名 |
| **ImageType** | 有 | 不列為相同 feature | Clip art / line drawing |
| **Color** | 有 | 不列為相同 feature | Dominant colors、accent color、黑白判定 |
| **Landmarks / Celebrities** | 有，domain-specific content | 不列為相同 feature | 地標 / 名人名稱及 confidence |
| **Adult** | 有 | 不列為相同 feature | Adult、racy、gory 判斷與分數 |
| **Smart Crop** | 智慧裁切縮圖能力 | Smart crop | 保留 area of interest；API 輸出形態不同 |
| **Read** | 獨立 asynchronous Read API | 同步影像文字擷取 | 文字與位置；不是把兩個版本的參數混寫 |

「不列為相同 feature」是版本功能比較，**不表示所有 Microsoft 模型都不能完成類似任務**。舊圖列出的顏色、品牌、成人內容，不能直接當成 4.0 feature enum。[Microsoft version comparison](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/overview-image-analysis)

### 6.3 86-category Taxonomy 與 Domain-specific Models

這裡保留舊題常見的 **Image Categorization** 語境；不用把所有 86 個分類死背。下列原筆記子類例子已對照官方 taxonomy；大類欄亦含方便閱讀的共同前綴。

| 大類 | 原筆記子類例子 |
|---|---|
| `animal_` | `animal_bird`, `animal_cat`, `animal_dog`, `animal_horse` |
| `building_` | `building_church`, `building_street`, `building_pillar` |
| `food_` | `food_bread`, `food_fastfood`, `food_pizza` |
| `indoor_` | `indoor_marketstore`, `indoor_room`, `indoor_venue` |
| `outdoor_` | `outdoor_city`, `outdoor_mountain`, `outdoor_road`, `outdoor_water` |
| `people_` | `people_baby`, `people_crowd`, `people_group`, `people_portrait` |
| `plant_` | `plant_flower`, `plant_tree`, `plant_leaves` |
| `text_` | `text_map`, `text_menu`, `text_sign` |
| `trans_` | `trans_bicycle`, `trans_bus`, `trans_car` |

**前綴不一定是獨立類別：** 官方列出 `trans_bicycle`、`trans_bus`、`trans_car`，但沒有單獨列出 `trans_`；原筆記的 `trans_` 應當作共同命名前綴，不能自行當成 API 回傳類別。[Categorization](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/concept-categorizing-images)、[Category taxonomy](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/category-taxonomy)

| 舊版 category 前綴 | 進一步辨識的 model | 必須保留的條件 |
|---|---|---|
| `people_` | `celebrities` | 人物照片不一定是名人；符合模型知識才可能命名 |
| `outdoor_` / `building_` | `landmarks` | 戶外或建築不一定是知名地標 |

**Domain-specific content** 文件區分兩種用法：直接指定領域模型分析，或在一般分析要求額外細節。原筆記前綴表是舊版搭配 category 的考題脈絡，不是呼叫所有領域模型都必經的流程。在 enhanced categorization 中先跑 taxonomy，再使用匹配的領域模型；此機制已由官方核實。穩定考點是該舊版提供 **celebrities / landmarks**，不是 `animal_dog`、clip art 或一般 Tags。[Domain-specific content](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/concept-detecting-domain-content)

### 6.4 舊題措辭與現行理解

| Current terminology | Legacy wording | Older-question answer / 解讀 |
|---|---|---|
| **Azure Vision in Foundry Tools**，依功能/API 判讀 | Azure Cognitive Services / Azure AI Services 下的 Computer Vision | 舊 umbrella name 不改變題目的工作負載；品牌更名不表示所有舊 API 已退休 |
| **Tags / descriptive metadata** | associating an image with metadata | **Tagging**；不是 taxonomy 分類 |
| 以實際模型能力選視覺方案 | specialized domain models when categorizing | 在 **Image Analysis 3.2** 語境選 **celebrities + landmarks**；不能套成所有現行模型的能力清單 |
| **Multimodal Responses input** | Chat Completions 的 `image_url` content type | Responses 使用 **`input_image`**；欄位內仍叫 `image_url`，不是只把連結文字交給模型 |

命名與跨服務背景見 [Legacy and Adjacent Topics](08_Legacy_and_Adjacent_Topics.md)；本節只保留 Vision 題目需要的差異。

## 7. Exam Keywords & Triggers

| If the question says... | Think... |
|---|---|
| one class for the entire image | **Multiclass Classification** |
| multiple independent labels | **Multilabel Classification** |
| locate each object / bounding box | **Object Detection** |
| assign a class to each pixel | **Semantic Segmentation** |
| separate individual objects with masks | **Instance Segmentation** |
| descriptive metadata / descriptive terms | **Tagging** |
| full sentence describing a picture | **Captioning** |
| predefined taxonomy / 86 categories | **Legacy Categorization** |
| clip art / line drawing | **Image Type Detection** |
| specialized domain models | 舊版 **Celebrities + Landmarks** |
| printed / handwritten text | **OCR / Read** |
| one-to-one identity match | **Face Verification** |
| identify among registered people | **Face Identification** |
| compare two images using Responses | `input_text` + 兩個 `input_image` |
| generate a new illustration | **Image Generation** |

## 8. Memory Rules

- **Classification = What；Detection = What + Where。**
- **Semantic = Pixel 的 class；Instance = Pixel 屬於哪個個體。**
- **Tags 是描述詞；Categories 是分類體系；Caption 是一句話。**
- **Detect 找臉；Identify 找人；Verify 比同一人。**
- **URL / Data URI → input_image.image_url。**
- **API 版本決定 feature；工作負載名稱不等於產品生命週期。**

## 9. Official Sources

核對日期：**2026-09-06**。下列為 English Microsoft 官方來源；未核實的原始細節已在正文標 **NEEDS VERIFICATION**。

- [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)：工作負載與實作範圍。
- [Azure Vision overview](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/overview)：現行服務入口。
- [Image Analysis overview](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/overview-image-analysis) 與 [Migration guide](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/migration-options)：版本功能、3.2 / 4.0 退休範圍。
- [Azure ML image labeling tasks](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-create-image-labeling-projects?view=azureml-api-2) 與 [Microsoft segmentation lesson](https://github.com/microsoft/AI-For-Beginners/blob/main/lessons/4-ComputerVision/12-Segmentation/README.md)：分類、偵測、分割的任務與標籤。
- [Image tagging](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/concept-tagging-images)：Tags 與 taxonomy 的區分。
- [Image categorization](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/concept-categorizing-images) 與 [Category taxonomy](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/category-taxonomy)：舊版分類。
- [Domain-specific content](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/concept-detecting-domain-content)：地標與名人模型。
- [Face overview](https://learn.microsoft.com/en-us/azure/ai-services/face/overview-identity) 與 [Face recognition](https://learn.microsoft.com/en-us/azure/ai-services/face/concept-face-recognition)：偵測、matching 與限制。
- [Vision release history](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/whats-new)：舊 API 生命週期。
- [Custom Vision migration](https://learn.microsoft.com/en-us/azure/ai-services/custom-vision-service/migration-options)：退休時間與替代選項。
- [Azure OpenAI Responses API](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/responses)：多張圖片、Data URI 與 content parts。
- [Image generation models](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/dall-e)：生成與編輯影像。
