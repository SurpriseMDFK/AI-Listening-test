# AI-Listening-test：免費 TTS 整合版

## 這個版本做了什麼

- 移除 ElevenLabs API Key 與付費 TTS API 呼叫。
- 使用瀏覽器 Web Speech API (`speechSynthesis`) 逐句朗讀。
- 可為 A、B 兩位說話者分別選擇裝置提供的英文語音。
- 可調整語速與句間停頓，支援停止及重播。
- 保留 JSON 格式測驗檔的 `dialogue`、`questions`、`options`、`answer` 結構。
- 渲染使用 `textContent`，避免將題目文字直接當 HTML 插入。
- 不需要伺服器、API Key、npm 或建置流程，可部署到 GitHub Pages。

## 安裝方式

1. 下載並解壓縮此 ZIP。
2. 將 `index.html` 放進你 GitHub Repo 的根目錄，或先在分支中測試。
3. 如果要取代原本版本，請先備份舊的 `index.html`，再用這份檔案替換。
4. 推送到 GitHub，確認 GitHub Pages 的來源分支與目錄設定正確。
5. 用手機瀏覽器打開網站，選取 JSON 格式的測驗檔，按「載入測驗」再按「播放對話」。

## 測驗檔格式

此版本沿用原本的基本結構。`.txt` 檔案內容仍須是合法 JSON，例如：

```json
{
  "dialogue": [
    {"speaker": "A", "text": "Good morning. How can I help you?"},
    {"speaker": "B", "text": "I'd like to reschedule our meeting."}
  ],
  "questions": [
    {
      "question": "What does the speaker want to do?",
      "options": ["Cancel a meeting", "Reschedule a meeting", "Book a flight", "Send an invoice"],
      "answer": 1
    }
  ]
}
```

`answer` 是 `options` 的零起始索引：第一個選項為 `0`，第二個為 `1`。

## 技術限制（請先閱讀）

### 1. 不需要付費 API，但不是所有語音都保證離線

Web Speech API 會使用瀏覽器／作業系統提供的語音。不同手機可能提供不同聲音；某些語音可能需要網路，且語音清單可能延遲載入。選單會以「裝置語音／可能使用網路」作為初步提示，但這不是離線能力的絕對保證。

### 2. 此版不是 MP3 生成器

`SpeechSynthesis` 直接朗讀，不會回傳可保存的 MP3 Blob。因此它適合即時播放與重複練習，但不適合要求下載音檔或將每句音檔隨題庫封裝的工作流程。

### 3. 如果要固定一致音色

瀏覽器 TTS 無法保證 Android、iOS、Windows 等裝置使用同一個音色。若必須統一音色，建議由題庫製作流程預先產生音檔，再將 MP3 與題庫一起發布；可用開源工具或允許免費使用的服務產生，但須遵守服務條款、配額與授權條件。

### 4. 為什麼沒有直接把 edge-tts 接到 GitHub Pages？

`rany2/edge-tts` 是呼叫 Microsoft 線上語音服務的 Python 用戶端，不是可直接從靜態網頁執行的 JavaScript 函式庫。若要讓瀏覽器使用它，通常需要一個後端代理／API 服務；公開的任意代理可能不穩定、涉及隱私與服務條款風險，不建議把它當成可靠的正式架構。GitHub Pages 本身只提供靜態檔案，不能直接執行 Python 服務。

## 建議測試

至少測試：
- Android Chrome 與 iPhone Safari。
- A、B 是否能選不同英文語音。
- 數字、日期、貨幣、縮寫及專有名詞的發音。
- 播放中按停止，再重新播放。
- 重新載入同一份測驗，不會呼叫付費 TTS API。
- 關閉網路後播放所選語音；如果無法播放，表示該語音不是可完全離線使用的選項。

## 後續升級路線

若系統語音品質不足，可保留本版作為免費備援，再增加：
1. 題庫音檔預生成：將固定 MP3 隨題庫發布。
2. 可自行部署的 Edge TTS 相容後端：只在你有權使用且可接受線上服務限制時啟用。
3. 本機神經網路 TTS：例如研究 sherpa-onnx/Piper 類方案，但瀏覽器 WASM 整合、模型下載大小、手機效能和授權都需要另外驗證。
