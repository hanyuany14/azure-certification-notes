# Text and Language Mistakes｜文字與語言錯題

完整概念見 [Text and Language](../knowledge/05_Text_and_Language.md)。

## Q04 — Language Detection 回傳欄位

![Original question](../assets/mistakes/Q04_source_image_9.png)

| 快速判斷 | 內容 |
|---|---|
| **答案** | **B. ISO 639-1 Code、C. Language Name、D. Score** |
| **線索** | `values returned`、`language detection` |
| **考點** | Language Detection output |
| **錯誤原因** | 把其他服務的輸出欄位混進 Language Detection |

**為什麼選 B、C、D：**Language Detection 的主要結果包含語言名稱、ISO 639-1 code 和 confidence score，例如 `English`、`en`、`0.99`。

| Option | 為什麼是／不是 |
|---|---|
| A. Bounding box coordinates | 影像或文件的位置資訊，與文字語言偵測無關。 |
| **B. ISO 639-1 Code** | **是。**例如 `en`。 |
| **C. Language Name** | **是。**例如 `English`。 |
| **D. Score** | **是。**`confidenceScore` 通常介於 0 和 1。 |
| E. Wikipedia URL | Entity Linking 的舊式結果可能含連結，不是 Language Detection。 |

**補充與延伸：**現行 REST 欄位常見 `name`、`iso6391Name`、`confidenceScore`；SDK 命名可能因語言而不同。

> **記法：Language Detection = name＋ISO code＋confidence。**

延伸：[Language detection output](../knowledge/05_Text_and_Language.md#language-detection-output)｜來源：[Overview](https://learn.microsoft.com/en-us/azure/ai-services/language-service/language-detection/overview)、[Call the API](https://learn.microsoft.com/en-us/azure/ai-services/language-service/language-detection/how-to/call-api)

---

## Q06 — Unknown Language 的 Confidence Score

![Original question](../assets/mistakes/Q06_source_image_11.png)

| 快速判斷 | 內容 |
|---|---|
| **現行答案** | **`0.0`；題目沒有正確選項** |
| **舊題庫答案** | **C. `NaN`** |
| **線索** | `unknown language name`、`confidence score` |
| **考點** | Current behavior vs legacy exam-bank behavior |
| **錯誤原因** | 把舊版 `NaN` 當成現行 API 規格 |

**為什麼是 `0.0`：**現行文件說明，無法判定語言時會回傳 `(Unknown)`、空的 ISO code，以及 `confidenceScore: 0.0`。

| Option | 現行為什麼不是 |
|---|---|
| A. `1` | 表示最高信心，和 unknown 相反。 |
| B. `-1` | 不在一般 confidence 的 0–1 範圍內。 |
| C. `NaN` | 只符合這份舊題庫的預期答案。 |
| D. `Unknown` | 是語言名稱的語意，不是數值 score。 |

**補充與延伸：**舊題若明確沿用這組選項，辨識其預期答案為 `NaN`；實作與現行知識題則記 `0.0`。

> **記法：現行 Unknown language → empty ISO code＋0.0。**

延伸：[Current vs Legacy](../knowledge/05_Text_and_Language.md#4-current-vs-legacy)｜來源：[Ambiguous content](https://learn.microsoft.com/en-us/azure/ai-services/language-service/language-detection/how-to/call-api#ambiguous-content)
