# AI-901 知識庫

本知識庫依 **2026-04-15 起生效的 AI-901 skills measured** 整理，內容於 **2026-09-06** 以英文 Microsoft Learn 複核。英文專有名詞保留為考試辨識用語，中文負責建立直覺。

## 建議閱讀順序

| 順序 | 模組 | 用途 |
|---:|---|---|
| 1 | [Responsible AI](knowledge/01_Responsible_AI.md) | 六大負責任 AI 原則 |
| 2 | [AI Models and Workloads](knowledge/02_AI_Models_and_Workloads.md) | 生成式模型、工作負載、模型選擇與設定 |
| 3 | [Microsoft Foundry](knowledge/03_Microsoft_Foundry.md) | Portal、部署、prompt、app、agent、SDK 與檢索 |
| 4 | [Text and Language](knowledge/04_Text_and_Language.md) | 文字分析與語言能力 |
| 5 | [Speech](knowledge/05_Speech.md) | Speech SDK 與多模態音訊 |
| 6 | [Computer Vision](knowledge/06_Computer_Vision.md) | 視覺理解、影像生成與 legacy Vision 題型 |
| 7 | [Content Understanding](knowledge/07_Content_Understanding.md) | 文件、圖片、音訊與影片資訊擷取 |
| 8 | [Legacy and Adjacent Topics](knowledge/08_Legacy_and_Adjacent_Topics.md) | Azure 基礎與舊題庫服務脈絡 |

目前考試權重：**Identify AI concepts and capabilities 40–45%**；**Implement AI solutions by using Microsoft Foundry 55–60%**。先讀 01–02 建立概念，再以 03–07 作為主要實作範圍；08 用於看懂舊題，不應取代現行產品文件。

## 錯題索引

| 模組 | 錯題筆記 |
|---|---|
| Responsible AI | [01 Responsible AI Mistakes](mistakes/01_Responsible_AI_Mistakes.md) |
| Microsoft Foundry | [03 Foundry Mistakes](mistakes/03_Foundry_Mistakes.md) |
| Text and Language | [04 Text and Language Mistakes](mistakes/04_Text_and_Language_Mistakes.md) |
| Speech | [05 Speech Mistakes](mistakes/05_Speech_Mistakes.md) |
| Computer Vision | [06 Computer Vision Mistakes](mistakes/06_Computer_Vision_Mistakes.md) |
| Content Understanding | [07 Content Understanding Mistakes](mistakes/07_Content_Understanding_Mistakes.md) |

原始材料沒有可歸入「AI Models and Workloads」的題目截圖，因此未建立空的 `02_AI_Models_Mistakes.md`。

## 目錄用途

| 目錄 | 內容 |
|---|---|
| `knowledge/` | 每個主要模組一份 canonical knowledge note |
| `mistakes/` | 依模組集中管理的原始題目、選項分析與考點 |
| `assets/knowledge/` | 對知識有幫助的原始投影片／圖表副本 |
| `assets/mistakes/` | 21 張原始題目截圖／補充截圖的逐檔副本，對應 19 題錯題記錄 |
| `source/` | 不修改的 Notion export、PDF 與全部原始圖片 |
| `raw notes/` | 遷移前原始匯出備份，同樣不修改 |

## 狀態閱讀方式

| 標記 | 意義 |
|---|---|
| **Current** | 目前可用且仍適合用現行文件學習 |
| **Preview** | 預覽功能；API 與可用性可能變動 |
| **Deprecated / Retiring** | 已不建議新採用，或已公布退場計畫 |
| **Retired** | 已退休；只保留舊題庫脈絡 |
| **Legacy exam-bank context** | 為理解舊題保留；不代表同名產品一定已退休 |
| **NEEDS VERIFICATION** | 官方文件不足、互相衝突，或原始材料已缺損，未自行猜測 |

錯題中的完整原理以 `knowledge/` 為準；`mistakes/` 只解釋該題為何對或錯。Microsoft 產品名稱、SDK 與 Preview 狀態會持續變動，後續更新應以 [current English AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) 和英文 Microsoft Learn 為優先依據。
