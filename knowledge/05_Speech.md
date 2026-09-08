# Speech and Audio｜語音與多模態音訊

查核日期：**2026-09-06** · 以英文 Microsoft Learn 為準 · [學習索引](../README.md)

## 1. Module Overview

| Item | Study notes |
| --- | --- |
| Official service/module name | **Azure Speech in Foundry Tools**；另比較 deployed multimodal audio models |
| AI-901 relevance | **High**：speech recognition、speech synthesis、回應 spoken prompts、Speech SDK 輕量應用；Custom Speech、translation、language identification：**Medium** |
| Current status | **Current** Speech 服務；現行 audio 模型已有 **GA** 選項；舊 audio-preview 範例與 API 版本另列於第 6 節，不能把整個音訊能力都稱 Preview |
| Main exam workloads | Audio → Text；Text → Audio；Audio prompt → generated Text / Audio |
| Primary Microsoft products | Azure Speech、Microsoft Foundry、Azure OpenAI audio-capable models |

範圍：[AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)。產品：[Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/overview)。

本頁 High / Medium 為依綱要安排的讀書優先度，並非官方獨立配分。優先讀 STT / TTS、SDK 物件責任與多模態 spoken prompt；客製模型細節作為相鄰情境。

## 2. Core Concepts

| English Term | 中文解釋 | What it does | Input | Output | Exam Keywords |
| --- | --- | --- | --- | --- | --- |
| **Speech Recognition / Speech to Text (STT)** | 把說話內容轉成文字 | 語音轉錄 | 麥克風、音訊檔／串流 | transcript | transcribe, recognize, captions |
| **Speech Synthesis / Text to Speech (TTS)** | 把文字變成合成語音 | 用指定 voice 朗讀文字 | 文字／SSML | synthesized audio | read aloud, voice, synthesize |
| **Speech Translation** | 翻譯口說內容 | 跨語言語音翻譯 | 來源語言語音 | 目標語言文字／語音 | translate spoken language |
| **Language Identification (LID)** | 判斷音訊講哪種語言 | 比對候選語言；可結合 STT / translation | 音訊、candidate languages | 語言識別結果 | identify spoken language |
| **Speaker Diarization** | 區分不同說話者的片段 | 標記誰在何時說話 | 多人音訊 | speaker 標記與片段 | who spoke when |
| **Multimodal Audio Model** | 理解音訊並產生回答 | 根據 spoken prompt 生成回應 | Text / Audio | 生成的 Text / Audio | spoken prompt, answer, modalities |
| **Custom Speech** | 改善特定領域辨識 | 評估／調整 base model 對領域用語等的辨識 | 支援的訓練資料 | 客製化 speech recognition model | domain vocabulary, acoustic data |

STT / TTS / translation 的服務邊界：[Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/overview)。LID 與候選語言：[Language identification](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-identification)。多模態生成：[Audio generation quickstart](https://learn.microsoft.com/en-us/azure/foundry/openai/audio-completions-quickstart)。結構化通話角色與摘要另見 [Content Understanding](07_Content_Understanding.md)，不要把一般 speaker label 直接當成 Agent / Customer 角色。

### SDK objects：服務設定與聲音來源分開

| English Term | 中文解釋 | What it does | Input | Output | Exam Keywords |
| --- | --- | --- | --- | --- | --- |
| **SpeechConfig** | Speech 服務設定 | 設定認證、region / endpoint、recognition language、synthesis voice 等 | 服務組態 | recognizer / synthesizer 使用的設定 | key, region, endpoint, voice |
| **AudioConfig** | 辨識用音訊來源 | 選麥克風、WAV 檔或輸入串流 | input source | recognizer 的音訊設定 | microphone, filename, stream |
| **SpeechRecognizer** | 辨識執行物件 | 使用 SpeechConfig + AudioConfig 做 STT | speech + audio config | recognition results / events | recognize |
| **AudioOutputConfig** | 合成音訊去向 | 指定喇叭、檔案或輸出串流 | output destination | synthesizer 的輸出設定 | default speaker, file output |
| **SpeechSynthesizer** | 合成執行物件 | 使用服務與輸出設定做 TTS | text + configuration | synthesis result / audio | speak_text_async |

依據：[STT quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-speech-to-text)、[TTS quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-text-to-speech)。

## 3. How It Works

### 工作流程（Workload flows）

| 需求 | 流程 | 例子／重要區分 |
| --- | --- | --- |
| 逐字記錄 | Audio → SpeechRecognizer → Text | 會議字幕；目標是辨識說出的內容 |
| 朗讀既有答案 | response_text → SpeechSynthesizer → AudioOutputConfig | 已有回答文字，指定 Ava 讀出並從喇叭播放 |
| 口說問題取得生成回答 | Audio prompt → deployed audio model → generated answer | 使用者問行程，模型產生文字與聲音回答；不是只轉錄問題 |
| 變更語言 | Spoken source language → Speech Translation → target language | 說英文，回傳中文翻譯 |

### 預設模型與客製化（Universal Language Model vs customization）

**Universal Language Model (ULM)** 是官方目前仍使用的 base-model 說明：以 Microsoft 資料訓練，涵蓋常見口語、方言及語音模式。先評估預設模型，再判斷是否需要 Custom Speech。[Custom Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/custom-speech-overview)

| Term | 層次 | 示意 | 該怎麼讀原筆記 |
| --- | --- | --- | --- |
| **Conversational** | 使用情境 | 自然對話、客服交談 | 保留為日常語音情境；不是名為 conversational 的必填 API 模式 |
| **Dictation** | 使用情境；SDK 也有對應設定 | 一人連續口述筆記 | `SpeechConfig.enable_dictation()` 僅支援 continuous recognition；不可把它當成訓練資料類型 |
| **Acoustic** | 聲學／錄音條件 | 背景噪音、裝置、特定環境音訊 | Custom Speech 可以使用的改善方向依語言／資料類型而異 |
| **Language** | 語言與詞彙 | 產品代號、專業領域術語 | 可用領域文字改善詞彙辨識 |
| **Pronunciation** | 字詞發音 | 人名或縮寫的特殊念法 | 支援情形查語言與訓練資料表，非所有語言皆相同 |

舊筆記以 Conversational / Dictation 對照 Acoustic / Language / Pronunciation，重點是**使用情境 vs 客製方向**；不需要背成「ULM 只有兩種模式」。`enable_dictation()` 的適用範圍則有明確 SDK 契約。[SpeechConfig reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)、[Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/overview)、[Language support](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-support)

### 音訊輸入／輸出組合（Audio modality matrix）

下表描述官方 audio quickstart 的支援組合；實際部署仍需符合該模型與 API 的支援，不能套用到任意文字模型。

| Input | Output | 學習示例 |
| --- | --- | --- |
| Text | Text + Audio | 輸入文字問題，回文字與口說回答 |
| Audio | Text | 用錄音提問，取得文字回答 |
| Audio | Text + Audio | WAV 提問，取得文字與 WAV 回答 |
| Text + Audio | Text | 同時提供指令與音訊，要求文字分析 |
| Text + Audio | Text + Audio | 用文字限定任務，再解讀錄音並口說回答 |

**輸入型態與輸出型態分開設定**：`messages[].content` 描述輸入；`modalities` 描述要模型回什麼。[Audio generation quickstart](https://learn.microsoft.com/en-us/azure/foundry/openai/audio-completions-quickstart)

## 4. Important API / SDK Patterns

### Speech SDK for Python：一次辨識

適用官方 **`azure-cognitiveservices-speech` Python SDK**（2026-09-06 以 Python API reference 核對）。需有效 Speech 資源與相容 WAV；設定好辨識語言，再建立 recognizer。以下程式為讀碼範例，未執行雲端呼叫。

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_config = speechsdk.SpeechConfig(
    subscription=os.environ["SPEECH_KEY"],
    region=os.environ["SPEECH_REGION"],
)
audio_config = speechsdk.audio.AudioConfig(filename="question.wav")
speech_config.speech_recognition_language = "en-US"
recognizer = speechsdk.SpeechRecognizer(
    speech_config=speech_config, audio_config=audio_config,
)
result = recognizer.recognize_once_async().get()
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print(result.text)
```

| API | 用途 | 易混淆處 |
| --- | --- | --- |
| `recognize_once_async()` | 辨識單一 utterance | 依結尾靜音或約 30 秒上限結束；不是長時間多句辨識 |
| `start_continuous_recognition_async()` | 啟動持續辨識 | 後續結果透過事件取得；需停止／結束處理 |
| `.get()` | 等待 `ResultFuture` 完成並取得結果 | 會阻塞等候；`recognize_once_async().get()` 不代表整段程式保持非阻塞 |
| `recognized` event | 接收 continuous recognition 的辨識結果 | 啟動操作完成不代表所有語音已辨識完 |

程式與行為：[SpeechRecognizer reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)、[AudioConfig reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python)、[ResultFuture.get](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultfuture?view=azure-python)。實作時也要處理 `NoMatch`、`Canceled` 等未成功結果，不要把空白文字視為成功辨識。[STT quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-speech-to-text)

### Speech SDK：指定 voice 與輸出

承接上面的 `speechsdk` 與 `speech_config`；`response_text` 為應用程式已產生的文字。

```python
response_text = "Your request has been received."
speech_config.speech_synthesis_voice_name = "en-US-Ava:DragonHDLatestNeural"
audio_output = speechsdk.audio.AudioOutputConfig(use_default_speaker=True)
synthesizer = speechsdk.SpeechSynthesizer(
    speech_config=speech_config, audio_config=audio_output,
)
result = synthesizer.speak_text_async(response_text).get()
```

| Setting | 意義 | 考試例子 |
| --- | --- | --- |
| `speech_synthesis_language = "en-US"` | 指定語言／locale | 不保證指定 Ava 人物聲線 |
| `speech_synthesis_voice_name` | 指定 voice identity | Ava / Jenny / Andrew 名稱需求看這個 |
| `AudioOutputConfig(use_default_speaker=True)` | 從預設喇叭播放 | play on default speaker |
| `AudioOutputConfig(filename="output.wav")` | 將音訊寫入檔案 | save to file；副檔名不取代音訊格式設定 |
| `set_speech_synthesis_output_format(...)` | 明確指定合成格式 | 例如 `SpeechSynthesisOutputFormat.Riff16Khz16BitMonoPcm`；和輸出去向是兩項設定 |
| `SpeechSynthesizer` | 實際執行合成 | configuration 物件本身不會朗讀 |
| `speak_text_async(response_text).get()` | 將文字合成語音並等結果 | 與 STT 的 recognize 分清 |

`en-US-Ava:DragonHDLatestNeural` 為官方明列 voice；**Current / GA**，`LatestNeural` 指向最新 DragonHD base model，不是固定不可變版本。個別 HD voices 可有不同 Preview / GA 狀態及區域支援。[HD voices](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/high-definition-voices)、[TTS quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-text-to-speech)

參數核對：[SpeechConfig](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)、[AudioOutputConfig](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python)。

### Audio Chat Completions：WAV → Base64 → input_audio

**現行 v1 模式**：使用 OpenAI Python SDK 的 `OpenAI` client，`base_url` 指向 Azure 資源的 `/openai/v1/`。`audio_deployment` 必須是支援音訊輸入與輸出的實際部署名稱，例如部署後自行命名的 `gpt-audio`；不是 Foundry project 名稱。[v1 API](https://learn.microsoft.com/en-us/azure/foundry/openai/api-version-lifecycle)、[模型能力表](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/concepts/models-sold-directly-by-azure)

```python
import base64
import os
from pathlib import Path
from openai import OpenAI

client = OpenAI(
    base_url=os.environ["AZURE_OPENAI_BASE_URL"],  # .../openai/v1/
    api_key=os.environ["AZURE_OPENAI_API_KEY"],
)
audio_deployment = os.environ["AZURE_OPENAI_AUDIO_DEPLOYMENT"]

encoded_audio = base64.b64encode(Path("question.wav").read_bytes()).decode("utf-8")
response = client.chat.completions.create(
    model=audio_deployment,
    modalities=["text", "audio"],
    audio={"voice": "alloy", "format": "wav"},
    messages=[{"role": "user", "content": [
        {"type": "input_audio", "input_audio": {
            "data": encoded_audio, "format": "wav",
        }},
    ]}],
)
audio_reply = response.choices[0].message.audio
if audio_reply is not None:
    print(audio_reply.transcript)
    wav_bytes = base64.b64decode(audio_reply.data)
```

| Field | 考試區分 |
| --- | --- |
| `input_audio.data` | Base64 音訊內容；上例不是 WAV 路徑或 image URL |
| `input_audio.format` | 輸入音訊格式，例如 wav |
| `modalities=["text"]` | 要文字輸出 |
| `modalities=["text", "audio"]` | 要文字與音訊輸出 |
| `audio.format` / `audio.voice` | 指定輸出音訊格式／聲音；與輸入的 format 分開 |
| `message.audio.data` / `message.audio.transcript` | 音訊回應資料與其文字逐字稿；不是 Responses 的 `output_text` |
| Chat Completions text part | 使用 `type: "text"`；不可混入其他 API 的 `input_text` 結構 |

欄位契約：[Azure OpenAI Chat REST](https://learn.microsoft.com/en-us/rest/api/microsoft-foundry/azureopenai/chat)。音訊輸入／輸出解析：[Audio quickstart](https://learn.microsoft.com/en-us/azure/foundry/openai/audio-completions-quickstart)。舊 quickstart 的 `AzureOpenAI(api_version="2025-01-01-preview", ...)` 與本頁 v1 client 是兩種版本模式；v1 不要求每次加日期型 `api-version`。部署與認證共通概念見 [Foundry](03_Microsoft_Foundry.md)。

## 5. Comparison / Common Confusions

| Compare | 正確區分 | Common trap |
| --- | --- | --- |
| STT vs TTS | Audio → Text vs Text → Audio | 有音訊需求不代表兩者都要 |
| Transcription vs Generative response | 辨識原話 vs 回答原話中的問題 | 回傳文字不保證是原音訊逐字稿 |
| SpeechConfig vs AudioConfig | 服務／辨識與聲音設定 vs 輸入來源 | **Correction：**endpoint / region / key 屬 SpeechConfig；麥克風／WAV 屬 AudioConfig |
| AudioConfig vs AudioOutputConfig | 辨識輸入 vs 合成輸出 | input microphone 與 output speaker 不同 |
| Language vs Voice name | 語言 vs 特定聲線 | 題目指定 Ava，只有 `en-US` 不夠 |
| Speaker vs File | 播放 vs 儲存 | default speaker 不是 file output |
| Once vs Continuous | 一段 utterance vs 多段事件 | async 不等於 continuous |
| Language identification vs Diarization | 說哪種語言 vs 哪個 speaker 的片段 | 語言相同仍可有多位 speaker |
| Speech LID vs Text detection | Audio input vs Text input | 文字語言分析見 [Text and Language](04_Text_and_Language.md) |
| `alloy` vs Azure Speech Ava | 不同 API／模型的 voice 選項 | 不把 Speech voice 字串直接套用到 audio model |

SDK correction 來源：[STT quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-speech-to-text)。語音回應 schema 來源：[Chat REST](https://learn.microsoft.com/en-us/rest/api/microsoft-foundry/azureopenai/chat)。

## 6. Current vs Legacy

| Material | Status | 維護方式 |
| --- | --- | --- |
| Azure Speech in Foundry Tools | **Current** | 使用目前正式服務名稱 |
| Azure Cognitive Services Speech | **Legacy / exam-bank terminology** | 舊品牌；目前服務稱 Azure Speech in Foundry Tools |
| `azure-cognitiveservices-speech` | **Current** SDK 套件名稱 | 歷史字串仍是正確 import / 套件名，不能替換成想像中的 foundry-speech 套件 |
| Universal Language Model | **Current** 官方描述 | 可解釋 base model，不擴寫成未驗證的封閉模式清單 |
| `gpt-audio` / `gpt-audio-mini` / `gpt-audio-1.5` | **Current / GA**（依特定版本） | 音訊＋文字生成已有 GA 選項；查模型與版本，不把所有 audio 模型叫 Preview |
| `gpt-4o-audio-preview` / `gpt-4o-mini-audio-preview` | **Preview** 名稱／舊範例 | 模型能力文件仍列出；舊 quickstart 不保證各區域現今可部署。精確可用性以模型版本及部署清單為準，不從名稱猜已 Retired |
| `AzureOpenAI` + 日期型 API version | **Legacy / exam-bank pattern**，不代表整個 client 已退役 | 保留讀舊題的語法；現行 v1 用 `OpenAI` + `/openai/v1/` |
| `en-US-Ava:DragonHDLatestNeural` | **Current / GA** | 不因其他 HD voice 是 Preview 就把 Ava 一起標 Preview |
| 原題 AudioConfig 選項／答案衝突 | 題目選取錯誤，**不是產品退役** | 原 Q48 第三列應為 No；原圖保留於錯題筆記 |

2026-09-06 核對：[Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/overview)、[Custom Speech](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/custom-speech-overview)、[HD voices](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/high-definition-voices)、[模型能力表](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/concepts/models-sold-directly-by-azure)、[模型生命週期表](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/model-retirement-schedule)。

| 名詞對照 | 作答方式 |
| --- | --- |
| **Current terminology** | Azure Speech in Foundry Tools；SDK 物件仍為 SpeechConfig / AudioConfig；音訊模型依具體 GA / Preview 版本區分 |
| **Legacy / exam-bank terminology** | Cognitive Services Speech；`gpt-4o-mini-audio-preview` 搭日期型 AzureOpenAI client |
| **What to answer if this wording appears on an older question** | WAV input 仍看 Base64 + input_audio；同時要聲音與文字就選兩種 modalities；指定 Ava + 預設喇叭就選 voice_name + default speaker。不要因品牌變更改掉正確 SDK 欄位 |

## 7. Exam Keywords & Triggers

| Trigger | Think | 關鍵操作／輸出 |
| --- | --- | --- |
| transcribe / captions / spoken words | Speech to Text | SpeechRecognizer |
| generated response text / read aloud | Text to Speech | SpeechSynthesizer |
| microphone / WAV input source | AudioConfig | 輸入來源 |
| Ava / Jenny / named voice | speech_synthesis_voice_name | 完整 voice 名稱 |
| default speaker / play immediately | AudioOutputConfig | use_default_speaker=True |
| many utterances / ongoing recognition | Continuous recognition | events + stop handling |
| WAV question + answer in text and audio | Audio model | input_audio + output modalities |
| domain-specific vocabulary | Custom Speech | 評估 base model 再客製 |
| identify spoken language | LID | candidate languages |

## 8. Memory Rules

- **SpeechConfig 管服務；AudioConfig 管輸入；AudioOutputConfig 管輸出。**
- **Recognize = 聽懂原話；Synthesize = 把文字讀出。**
- **Once 一句；Continuous 多句；get 等結果。**
- **Language = 說哪種語言；Voice = 用哪個聲音。**
- **Input audio → Base64 → input_audio；Output audio → modalities + audio options。**
- **Speaker = 播放；File = 儲存；Transcript ≠ generated answer。**

## 9. Official Sources

本次查核 **2026-09-06**。範例未執行雲端呼叫；短程式用於辨識欄位與物件責任。

| Source | 用途 |
| --- | --- |
| [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901) | Speech / multimodal workload 範圍 |
| [Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/overview) | STT、TTS、translation、customization |
| [STT quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-speech-to-text) | SDK 建構與單次辨識 |
| [Recognize speech](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/how-to-recognize-speech) | 音訊來源與 continuous recognition |
| [TTS quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-text-to-speech) | 合成與 voice / output 設定 |
| [HD voices](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/high-definition-voices) | Ava 完整 voice 名稱、GA / Preview |
| [Custom Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/custom-speech-overview) | ULM base model、領域客製 |
| [Language support](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-support) | 語言與訓練資料支援差異 |
| [Language identification](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-identification) | 音訊候選語言判斷 |
| [Audio generation quickstart](https://learn.microsoft.com/en-us/azure/foundry/openai/audio-completions-quickstart) | Preview API、WAV、modalities |
| [Azure OpenAI Chat REST](https://learn.microsoft.com/en-us/rest/api/microsoft-foundry/azureopenai/chat) | input_audio 與音訊 response schema |
| [SpeechConfig reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) | 服務認證、dictation、音訊格式 |
| [AudioConfig reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) | 音訊來源參數 |
| [AudioOutputConfig reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python) | 喇叭與檔案參數 |
| [SpeechRecognizer reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) | once / continuous 的精確行為 |
| [ResultFuture reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultfuture?view=azure-python) | get 等待結果 |
| [Azure OpenAI v1](https://learn.microsoft.com/en-us/azure/foundry/openai/api-version-lifecycle) | 現行 OpenAI client 與 endpoint |
| [模型能力表](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/concepts/models-sold-directly-by-azure) | 音訊模型功能 |
| [模型生命週期表](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/model-retirement-schedule) | 模型版本 GA / Preview 與支援狀態 |
