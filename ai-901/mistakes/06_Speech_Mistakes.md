# Speech Mistakes｜語音錯題

## Q15 — WAV 輸入與文字／音訊輸出

![Original question](../assets/mistakes/Q15_source_image_20.png)

| 重點 | 答案 |
|---|---|
| **正確答案** | **C. Base64 WAV → `input_audio`，並要求 text + audio modalities** |
| **題目線索** | `recorded WAV`、`both text and WAV audio output` |
| **考點** | Audio-capable model 的輸入格式與輸出 modalities |

**為什麼選 C：** 服務不能直接讀取本機檔名。WAV 內容要先轉成 Base64，標示為 `input_audio`；若要同時取得文字和聲音，輸出 modalities 必須同時包含 text 與 audio。

| 選項 | 為什麼選／不選 |
|---|---|
| A | 只有 text modality，不會回傳音訊。 |
| B | 檔名不是音訊內容，服務無法存取本機檔案。 |
| **C** | 同時提供真正的音訊內容，並要求兩種輸出。 |
| D | WAV bytes 不能當成一般文字輸入。 |

**補充與延伸：** 這題考的是支援 audio 的模型 API，不是 Speech SDK。模型名稱與 API 版本可能改變，但判斷方式不變：先確認音訊是否真的傳入，再確認輸出 modalities。

> **記憶：** 本機音訊 → Base64 `input_audio`；要聲音回覆 → 加入 audio modality。

- 延伸筆記：[Speech｜Audio modality matrix](../knowledge/06_Speech.md#audio-modality-matrix)
- 官方來源：[Azure OpenAI audio generation quickstart](https://learn.microsoft.com/en-us/azure/foundry/openai/audio-completions-quickstart)

---

## Q16 — SpeechConfig、AudioConfig 與辨識模式

![Original question](../assets/mistakes/Q16_source_image_21.png)

![原始補充截圖：題目解說](../assets/mistakes/Q16_new_rationale_image_23.png)

| 重點 | 答案 |
|---|---|
| **正確答案** | **No / Yes / No** |
| **題目線索** | `long-running`、`.get()`、`AudioConfig` |
| **考點** | 單次／持續辨識與 SDK 物件責任 |

| 敘述 | 判斷 | 原因 |
|---|---:|---|
| `recognize_once_async()` 用於長時間、多段語音 | **No** | 它只辨識一個 utterance；多段語音要用 continuous recognition。 |
| `recognize_once_async().get()` 取得單一 utterance 的結果 | **Yes** | `.get()` 會等待非同步操作完成並取回結果。 |
| `AudioConfig` 保存 endpoint／region／key | **No** | 連線資料屬於 `SpeechConfig`；`AudioConfig` 指定麥克風、檔案或輸出裝置。 |

**補充與延伸：** 方法名稱有 `_async`，不代表呼叫 `.get()` 後仍不阻塞；`.get()` 會等到該次辨識完成。Continuous recognition 的結果則透過事件陸續取得，最後還要停止辨識。

> **記憶：** `SpeechConfig` 管服務；`AudioConfig` 管聲音；Once 一段、Continuous 多段。

- 延伸筆記：[Speech SDK 工作流程](../knowledge/06_Speech.md#speech-sdk-服務設定與聲音來源分開)
- 官方來源：[Speech-to-text quickstart](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/get-started-speech-to-text)、[AudioConfig class](https://learn.microsoft.com/en-us/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig)

---

## Q17 — 指定 TTS Voice 並直接播放

![Original question](../assets/mistakes/Q17_source_image_22.png)

| 重點 | 答案 |
|---|---|
| **正確答案** | **D. `speech_synthesis_voice_name` + default speaker** |
| **題目線索** | `Ava`、`play ... on the default speaker` |
| **考點** | Voice selection 與 audio output destination |

**為什麼選 D：** 題目指定 Ava，因此要設定完整的 `speech_synthesis_voice_name`；題目又要求直接播放，因此輸出要設為 default speaker。

| 選項 | 為什麼選／不選 |
|---|---|
| A | Voice 正確，但 file output 只會寫檔。 |
| B | Speaker 正確，但 locale 只指定語言，無法保證使用 Ava。 |
| C | Voice 與輸出目的地都不符合。 |
| **D** | 同時指定 Ava 與預設喇叭。 |

**補充與延伸：** Language／locale 決定語言範圍；voice name 才會選定特定聲音。實作時仍要確認該 voice 在目標區域是否可用。

> **記憶：** 誰來說 → Voice Name；播到哪裡 → AudioOutputConfig。

- 延伸筆記：[Speech SDK：指定 voice 與輸出](../knowledge/06_Speech.md#speech-sdk指定-voice-與輸出)
- 官方來源：[Speech synthesis](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/how-to-speech-synthesis)、[Language and voice support](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-support?tabs=tts)
