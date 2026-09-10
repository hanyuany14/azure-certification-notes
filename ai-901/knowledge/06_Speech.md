# Speech and Audio｜語音與多模態音訊

先看題目需要的是**轉錄、朗讀、翻譯、辨識語言，還是回答語音問題**。相同的 audio input，可能對應不同工作負載。

## 1. Core Concepts

| Capability | Azure service / product | Input → Output | 例子與 Exam keywords |
|---|---|---|---|
| **Speech Recognition / Speech to Text (STT)** | Azure Speech in Foundry Tools | Audio → Transcript | 會議轉錄、字幕；transcribe、recognize |
| **Speech Synthesis / Text to Speech (TTS)** | Azure Speech in Foundry Tools | Text / SSML → Audio | 朗讀通知；voice、read aloud、synthesize |
| **Speech Translation** | Azure Speech in Foundry Tools | 語音 → 另一語言的文字或語音 | 即時口譯；translate spoken language |
| **Language Identification (LID)** | Azure Speech in Foundry Tools | Audio＋candidate languages → Language | 判斷音訊使用哪種語言；identify spoken language |
| **Speaker Diarization** | Azure Speech in Foundry Tools | 多人音訊 → Speaker labels＋transcript | 標示誰在何時說話；who spoke when |
| **Custom Speech** | Azure Speech in Foundry Tools | 領域資料 → Custom STT model | 改善專業術語或特定環境辨識；domain vocabulary |
| **Call center transcription / analytics** | Azure Speech＋Azure Language in Foundry Tools | 通話 → Transcript＋speaker labels＋analysis | 客服錄音、PII redaction、sentiment、summary |
| **Multimodal Audio Model** | Audio-capable model in Microsoft Foundry | Text / Audio → Generated text / audio | 聽懂口說問題並回答；spoken prompt、modalities |

> **Speech transcription vs audio model：**STT 的輸出是使用者說話內容的逐字稿；audio-capable model 可以理解問題並生成回答。兩者都有 audio input，但目的不同。

## 2. Workload flows 與 SDK objects

### Speech to Text：服務設定＋輸入來源＋辨識器

```text
SpeechConfig + AudioConfig → SpeechRecognizer → Recognition result / events
```

| SDK object | 負責什麼 | Example |
|---|---|---|
| **SpeechConfig** | 認證、region / endpoint、辨識語言等服務設定 | `speech_recognition_language = "en-US"` |
| **AudioConfig** | 指定要辨識的音訊來源 | 麥克風、WAV 檔或 input stream |
| **SpeechRecognizer** | 執行 speech-to-text | Audio → recognized text |

例子：會議 WAV 轉錄時，`SpeechConfig` 指定服務與語言，`AudioConfig` 指定檔案，`SpeechRecognizer` 執行辨識。

### Text to Speech：服務設定＋輸出去向＋合成器

```text
Text + SpeechConfig + AudioOutputConfig → SpeechSynthesizer → Audio
```

| SDK object | 負責什麼 | Example |
|---|---|---|
| **SpeechConfig** | 認證、region / endpoint、voice 與輸出格式 | `speech_synthesis_voice_name = "en-US-Ava:DragonHDLatestNeural"` |
| **AudioOutputConfig** | 指定合成音訊要送去哪裡 | 預設喇叭、檔案或 output stream |
| **SpeechSynthesizer** | 執行 text-to-speech | Text → synthesized audio |

例子：朗讀系統回覆時，先以 `speech_synthesis_voice_name` 選 Ava，再用 `AudioOutputConfig` 指定從喇叭播放。

> **記法：**`SpeechConfig` 管服務與語音設定；`AudioConfig` 管 STT 輸入；`AudioOutputConfig` 管 TTS 輸出。

### 其他工作負載

| 需求 | Flow | 判斷重點 |
|---|---|---|
| 口說內容翻譯 | Audio → Speech Translation → Target text / audio | 目標是換語言 |
| 判斷口說語言 | Audio＋candidate languages → LID → Language | 不等於文字 Language Detection |
| 區分多人發言 | Multi-speaker audio → Diarization → Speaker labels | Speaker label 不一定等於 Agent / Customer 業務角色 |
| 分析客服通話 | Call audio → Batch transcription / diarization → PII、sentiment、summary | Speech 負責轉錄與分離 speakers；Language 負責文字分析 |
| 回答口說問題 | Audio prompt → Deployed audio model → Generated answer | 不是只把問題轉成逐字稿 |

## 3. 預設模型與客製化（Universal Language Model vs customization）

**Universal Language Model (ULM)** 是 Azure Speech 的基礎 speech-to-text model。先使用真實音訊評估 base model；若專業詞彙、發音、噪音或錄音環境造成明顯錯誤，再考慮 **Custom Speech**。

| Concept | 簡化理解 | 例子 |
|---|---|---|
| **Base model / ULM** | Microsoft 預先訓練的通用辨識模型 | 一般會議與日常對話 |
| **Language data** | 改善領域詞彙與文字模式 | 產品名稱、醫療術語 |
| **Structured text / pronunciation** | 指定特殊發音與顯示文字格式 | 人名、縮寫、品牌名稱 |
| **Audio＋reference transcriptions** | 改善特定錄音條件下的辨識 | 工廠噪音、特殊麥克風環境 |
| **Phrase list** | 在執行時提高特定詞語的辨識權重 | 少量人名或地名；不需要訓練模型 |

ULM 並不是固定附帶兩個名為 Conversational 和 Dictation 的獨立 base models；在舊教材中，兩者主要描述 ULM 可處理的語音情境：

| Scenario | 說話方式 | Example / exam distinction |
|---|---|---|
| **Conversational speech** | 兩人以上自然交談，可能互相打斷、語句較口語 | 會議、客服通話；常搭配 diarization |
| **Dictation** | 通常由單人清楚地對裝置口述，希望直接形成文字 | 口述 email、報告或筆記；Microsoft 365 Dictate |

> **Dictation 記法：**使用者不是在和另一個人聊天，而是把「要寫下來的文字」念給系統聽。Speech SDK 的 `SpeechConfig.enable_dictation()` 用於啟用 dictation，且只支援 continuous recognition；它不代表切換到另一個 ULM 模型。

文字、發音及帶參考逐字稿的音訊才是客製模型的改善資料。Custom Speech 的資料支援會因 locale 而異。

## 4. Important API / SDK Patterns

### Speech SDK for Python：一次辨識

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_config = speechsdk.SpeechConfig(
    subscription=os.environ["SPEECH_KEY"],
    region=os.environ["SPEECH_REGION"],
)
speech_config.speech_recognition_language = "en-US"
audio_config = speechsdk.audio.AudioConfig(filename="question.wav")
recognizer = speechsdk.SpeechRecognizer(
    speech_config=speech_config,
    audio_config=audio_config,
)

result = recognizer.recognize_once_async().get()
print(result.text)
```

| API / event | 用途 | 必記區分 |
|---|---|---|
| `recognize_once_async()` | 辨識單一 utterance | 依結尾靜音或約 30 秒上限結束；不是長時間多句辨識 |
| `start_continuous_recognition_async()` | 啟動持續辨識 | 後續結果透過事件取得；需要停止／結束處理 |
| `.get()` | 等待 `ResultFuture` 完成並取得結果 | 會阻塞等候；加了 `_async` 不代表整段程式都非阻塞 |
| `recognized` event | 接收 continuous recognition 的辨識結果 | 啟動操作完成不代表所有語音已辨識完 |

### Speech SDK：指定 voice 與輸出

```python
speech_config.speech_synthesis_voice_name = "en-US-Ava:DragonHDLatestNeural"
audio_output = speechsdk.audio.AudioOutputConfig(use_default_speaker=True)
synthesizer = speechsdk.SpeechSynthesizer(
    speech_config=speech_config,
    audio_config=audio_output,
)

result = synthesizer.speak_text_async("Your request is complete.").get()
```

| Setting / object | 作用 | Exam trigger |
|---|---|---|
| `speech_synthesis_language` | 指定語言／locale | 只指定語言，沒有指定人物聲線 |
| `speech_synthesis_voice_name` | 指定完整 voice identity | Ava、Jenny、Andrew 等 named voice |
| `AudioOutputConfig(use_default_speaker=True)` | 從預設喇叭播放 | play on default speaker |
| `AudioOutputConfig(filename="output.wav")` | 儲存到檔案 | save audio to file |
| `set_speech_synthesis_output_format(...)` | 指定音訊編碼格式 | 與播放／存檔去向是不同設定 |
| `SpeechSynthesizer` | 執行語音合成 | `speak_text_async(...)` |

### Audio modality matrix

> **這不是 Speech SDK 設定表。**它描述支援 audio 的已部署多模態模型，其 input 與 output modality 組合。實際支援仍取決於模型與 API。

| Model input | Requested output | 例子 |
|---|---|---|
| Text | Text＋Audio | 輸入文字問題，取得文字與口說回答 |
| Audio | Text | 用錄音提問，取得生成的文字回答 |
| Audio | Text＋Audio | 用錄音提問，取得文字與音訊回答 |
| Text＋Audio | Text | 用文字指定任務，再分析錄音並回傳文字 |
| Text＋Audio | Text＋Audio | 用文字指定任務，再以文字和聲音回答 |

在 audio model API 中：輸入內容放在 `messages[].content`；想要的輸出放在 `modalities`。這些欄位不屬於 `SpeechRecognizer` 或 `SpeechSynthesizer`。

### Audio Chat Completions：WAV → Base64 → input_audio

```python
import base64
from pathlib import Path

encoded_audio = base64.b64encode(
    Path("question.wav").read_bytes()
).decode("utf-8")

response = client.chat.completions.create(
    model=audio_deployment,
    modalities=["text", "audio"],
    audio={"voice": "alloy", "format": "wav"},
    messages=[{"role": "user", "content": [{
        "type": "input_audio",
        "input_audio": {"data": encoded_audio, "format": "wav"},
    }]}],
)
```

| Field | 用途 |
|---|---|
| `input_audio.data` | Base64 音訊內容，不是檔案路徑 |
| `input_audio.format` | 輸入音訊格式 |
| `modalities=["text"]` | 只要求文字輸出 |
| `modalities=["text", "audio"]` | 要求文字與音訊輸出 |
| `audio.voice` / `audio.format` | 設定輸出聲音與格式，和 input format 分開 |

## 5. Comparison / Common Confusions

| Compare | Difference |
|---|---|
| **STT vs TTS** | Audio → Text vs Text → Audio |
| **Transcription vs generated response** | 記錄原話 vs 理解問題後產生回答 |
| **SpeechConfig vs AudioConfig** | 服務／語言／voice 設定 vs STT 音訊來源 |
| **AudioConfig vs AudioOutputConfig** | 辨識輸入 vs 合成輸出 |
| **Language vs voice name** | 語言 locale vs 特定人物聲線 |
| **Once vs continuous** | 單一 utterance vs 多段事件式辨識 |
| **LID vs diarization** | 判斷說哪種語言 vs 判斷哪位 speaker 在說話 |
| **Speech LID vs text Language Detection** | Audio input vs text input |
| **Speech SDK voice vs audio model voice** | Ava 等 Speech voice vs alloy 等模型 voice；不可混用名稱 |

## 6. Current vs Legacy

| Term / capability | Status | 快速理解 |
|---|---|---|
| **Azure Speech in Foundry Tools** | **Current** | 現行正式服務名稱 |
| `azure-cognitiveservices-speech` | **Current** | Python Speech SDK 的現行 package / import 名稱 |
| **Universal Language Model** | **Current** | Azure Speech base model 的官方說明 |
| **Conversational / Dictation model choice** | **Legacy exam-bank context** | 舊教材的情境分類；現行文件不將兩者列成固定的兩個 ULM base models |
| Audio-capable models | **Current；依模型版本判斷 GA / Preview** | 不把所有 audio model 一律視為 Preview |
| `gpt-4o-audio-preview` 等 preview 名稱 | **Preview / legacy sample context** | 看到舊範例時依指定模型與 API 作答 |
| `AzureOpenAI`＋日期型 API version | **Legacy exam-bank pattern** | 現行 v1 範例可使用 `OpenAI` 與 `/openai/v1/` |
| Azure Cognitive Services Speech | **Legacy terminology** | 舊品牌名稱；概念可對應現行 Azure Speech |

## 7. Quick Memory Rules

- **Recognize = 聽寫；Synthesize = 朗讀。**
- **SpeechConfig 管服務；AudioConfig 管輸入；AudioOutputConfig 管輸出。**
- **Once 一句；Continuous 多句；`.get()` 等結果。**
- **Language 是語言；Voice 是特定聲線。**
- **Audio model 的 input / modalities 不是 Speech SDK 的 AudioConfig。**
- **先測 base model，不足時再做 Custom Speech。**

## 8. Official Sources

核對日期：**2026-09-10**。

- [AI-901 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ai-901)
- [Azure Speech in Foundry Tools overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/overview)
- [Speech to text quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-speech-to-text)
- [SpeechRecognizer Python reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)
- [Text to speech quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-text-to-speech)
- [SpeechConfig Python reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)
- [AudioConfig Python reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python)
- [AudioOutputConfig Python reference](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python)
- [Custom Speech overview](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/custom-speech-overview)
- [Post-call transcription and analytics](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/call-center-quickstart)
- [Language identification](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-identification)
- [Audio generation quickstart](https://learn.microsoft.com/en-us/azure/foundry/openai/audio-completions-quickstart)
- [Azure OpenAI Chat REST](https://learn.microsoft.com/en-us/rest/api/microsoft-foundry/azureopenai/chat)
