# Content Understanding Mistakes｜內容理解錯題

## Q07 — 表單欄位與重複列

![Original question](../assets/mistakes/Q07_source_image_12.png)

| 重點 | 答案 |
|---|---|
| **正確答案** | **D. Field extraction** |
| **題目線索** | `structured JSON`、`fields`、`repeated table rows` |
| **考點** | 一般內容擷取與 schema 欄位擷取的差異 |

**為什麼選 D：** 題目要的是具名欄位與重複的 line items。Field extraction 會依 schema 回傳結構化欄位；重複列可表示為 object array。

| 選項 | 為什麼選／不選 |
|---|---|
| A. Markdown output | 保留易讀的全文與結構，但不是指定的業務欄位 JSON。 |
| B. Classification | 判斷文件類別，不抽取欄位值。 |
| C. Content extraction | 擷取文字、版面、表格等一般內容，未必映射到指定 schema。 |
| **D. Field extraction** | 依 schema 取得具名欄位與重複列。 |

**補充與延伸：** 要閱讀、索引全文時看 Markdown／content extraction；要 invoice number、customer name、line items 等固定欄位時看 field extraction。

> **記憶：** 要全文 → Content extraction；要欄位 JSON → Field extraction。

- 延伸筆記：[Content Understanding｜Field schema](../knowledge/08_Content_Understanding.md#field-schema)
- 官方來源：[Analyzer reference](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/analyzer-reference)

---

## Q08 — 複雜表單使用 Schema-defined Fields

![Original question](../assets/mistakes/Q08_source_image_13.png)

![原始補充截圖：正確選項](../assets/mistakes/Q08_new_correct_answer_image_25.png)

| 重點 | 答案 |
|---|---|
| **正確答案** | **Schema-defined fields and relationships** |
| **題目線索** | `handwritten`、`visually complex`、`structured information` |
| **考點** | 用 schema 定義抽取目標 |

**為什麼選 schema-defined fields：** Schema 直接描述欄位名稱、型別、意義及巢狀關係。即使表單版面或標籤稍有變動，analyzer 仍知道要輸出什麼結構。

| 選項 | 為什麼選／不選 |
|---|---|
| Exact label matching | 標籤文字或手寫方式改變就容易失效。 |
| Fixed page coordinates | 欄位位置改變便不可靠。 |
| **Schema-defined fields and relationships** | 定義真正需要的欄位與資料結構。 |
| Source file normalization | 只是輸入前處理，沒有定義抽取結果。 |

**補充與延伸：** 陣列適合表示重複資料，nested objects 適合表示欄位關係。Schema 是輸出 contract；normalization 是讓輸入較一致，兩者用途不同。

> **記憶：** 版面會變也要固定輸出 → 定義 schema，不背座標。

- 延伸筆記：[Field schema](../knowledge/08_Content_Understanding.md#field-schema)
- 官方來源：[Analyzer reference](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/analyzer-reference)、[Best practices](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/best-practices)

---

## Q10 — Video Transcript 與 Key Frames

![Original question](../assets/mistakes/Q10_source_image_15.png)

| 重點 | 答案 |
|---|---|
| **正確答案** | **A. `transcriptPhrases`、C. `keyFrameTimesMs`** |
| **題目線索** | `speaker-attributed`、`start and end times`、`representative visual frames` |
| **考點** | AudioVisual 內建輸出欄位 |

**為什麼選 A、C：** `transcriptPhrases` 記錄說話者、內容與起訖時間；`keyFrameTimesMs` 記錄代表性畫面的時間點。

| 選項 | 為什麼選／不選 |
|---|---|
| **A. `transcriptPhrases`** | 對應帶 speaker 與時間的逐字稿。 |
| B. `cameraShotTimesMs` | 記錄鏡頭切換，不是代表性 key frames。 |
| **C. `keyFrameTimesMs`** | 對應代表畫面的時間戳。 |
| D. `width`／`height` | 只是影片尺寸。 |
| E. `fields` | 是自訂 schema 結果，不是上述內建時間元素。 |

**補充與延伸：** JSON 文件常用 camelCase；Python SDK 物件可能顯示 snake_case，例如 `key_frame_times_ms`。題目若問服務回應欄位，通常依 JSON 名稱作答。

> **記憶：** 誰在何時說什麼 → `transcriptPhrases`；代表畫面在哪裡 → `keyFrameTimesMs`。

- 延伸筆記：[Audio and video built-in output](../knowledge/08_Content_Understanding.md#audio-and-video-built-in-output)
- 官方來源：[AudioVisualContent interface](https://learn.microsoft.com/en-us/javascript/api/@azure/ai-content-understanding/audiovisualcontent?view=azure-node-latest)

---

## Q14 — Call Center 預建 Analyzer

![Original question](../assets/mistakes/Q14_source_image_19.png)

| 重點 | 答案 |
|---|---|
| **正確答案** | **C. `prebuilt-callCenter`** |
| **題目線索** | `agent/customer-attributed`、`summary`、`sentiment`、`minimal configuration` |
| **考點** | 客服通話的預建 analyzer |

**為什麼選 C：** `prebuilt-callCenter` 已針對客服通話提供 Agent／Customer 角色逐字稿、摘要與 sentiment，最符合 minimal configuration。

| 選項 | 為什麼選／不選 |
|---|---|
| A. Customized `prebuilt-audioSearch` | 可以再客製，但不是最少設定。 |
| B. Custom audio analyzer | 要自行設計 schema，設定較多。 |
| **C. `prebuilt-callCenter`** | 已包含客服情境需要的預建輸出。 |
| D. `prebuilt-audioSearch` | 偏一般音訊搜尋，不含完整客服預設欄位。 |

**補充與延伸（Current）：** `prebuilt-callCenter` 是 Content Understanding 的 analyzer ID，不是另一個獨立 Azure 服務。若只做逐字稿，也可能使用 Azure Speech；題目同時要求摘要、情緒與最少設定時，才明確指向此 analyzer。

> **記憶：** 客服 + 角色 + 摘要 + 情緒 + 最少設定 → `prebuilt-callCenter`。

- 延伸筆記：[Audio analyzers](../knowledge/08_Content_Understanding.md#audio-analyzers)
- 官方來源：[Content Understanding audio solutions](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/audio/overview)

---

## Q18 — 圖片描述搜尋與產品分類

![Original question](../assets/mistakes/Q18_source_image_24.png)

| 重點 | 答案 |
|---|---|
| **正確答案** | **A. Markdown → search content；B. schema field 使用 `classify`** |
| **題目線索** | `descriptive text`、`indexed for image search`、`structured product category` |
| **考點** | Markdown、schema field 與 source location 的用途 |

**為什麼選 A、B：** 題目同時要求「可搜尋的描述文字」和「固定產品類別」。Markdown 適合作為 search index 的文字內容；`classify` field 則輸出結構化 category。

| 選項 | 為什麼選／不選 |
|---|---|
| **A. Markdown → content field** | 讓描述性文字可被搜尋。 |
| **B. Schema field + `classify`** | 取得預定義的產品類別。 |
| C. Source locations → content field | Source location 用來追溯證據位置，不是搜尋內容。 |
| D. Content Safety moderation | 偵測有害內容，不產生產品描述或分類。 |

**補充與延伸：** Markdown 是人可讀、可索引的內容；fields 是結構化值；source locations 是結果證據。三者可一起出現在結果中，但不能互換。

> **記憶：** Markdown 用來搜尋；`classify` 取得固定類別；source 用來追溯。

- 延伸筆記：[圖片索引：Markdown、Fields、Source](../knowledge/08_Content_Understanding.md#圖片索引markdownfieldssource-各做什麼)
- 官方來源：[Image overview](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/image/overview)、[Analyzer reference](https://learn.microsoft.com/en-us/azure/ai-services/content-understanding/concepts/analyzer-reference)
