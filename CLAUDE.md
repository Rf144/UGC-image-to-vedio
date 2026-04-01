# 專案背景

電商 UGC 影片自動化工具，單一 `index.html`，部署於 Netlify。

- **線上網址：** https://frabjous-peony-72f93f.netlify.app
- **GitHub：** https://github.com/Rf144/-
- **本地：** `http://localhost:5500`（需先執行 `npx serve -p 5500`）

## 使用者資訊

- 非工程師背景，需要完整引導
- 使用繁體中文溝通
- Windows 11，VS Code，Chrome
- GitHub 帳號：Rf144

## 開發規則

1. 每次修改完若需上線：`git add index.html && git commit -m "..." && git push && netlify deploy --prod --dir ugc-video-tool`
2. **不可把 API Key 寫進程式碼**（GitHub 會自動偵測並撤銷）
3. API Key 一律用 localStorage 儲存
4. 本地測試用 `npx serve -p 5500`，不需每次部署 Netlify（免費額度有限）

## 部署指令

```bash
export PATH="/c/Users/Lg/AppData/Roaming/npm:/c/Program Files/nodejs:$PATH"
cd "c:/Users/Lg/OneDrive/桌面/VS Code"
git add ugc-video-tool/index.html && git commit -m "說明" && git push
netlify deploy --prod --dir ugc-video-tool
```

## 技術架構

- 單一 `index.html`（含所有 CSS + JavaScript）
- OpenRouter API：圖片分析、產生 Prompt、提取關鍵字
- kie.ai API：影片生成

### kie.ai API — 通用模型（Sora / Kling / Hailuo / Wan）

| 用途 | 端點 |
|------|------|
| 圖片上傳 | `POST https://kieai.redpandaai.co/api/file-base64-upload` |
| 建立任務 | `POST https://api.kie.ai/api/v1/jobs/createTask` |
| 查詢任務 | `GET https://api.kie.ai/api/v1/jobs/recordInfo?taskId={taskId}` |

- 狀態欄位：`data.state`（值：`waiting` / `queuing` / `generating` / `success` / `fail`）
- 影片網址：解析 `data.resultJson` → `resultUrls[0]`

### kie.ai API — Runway

| 用途 | 端點 |
|------|------|
| 建立任務 | `POST https://api.kie.ai/api/v1/runway/generate` |
| 查詢任務 | `GET https://api.kie.ai/api/v1/runway/record-info?taskId={taskId}` |

- Request body: `{ prompt, duration: 10, quality: "720p", aspectRatio: "9:16", imageUrl }`
- 狀態欄位：`data.state`（同通用）
- 影片網址：`data.videoInfo.videoUrl`
- **注意：** `/runway/record-detail` 是 404，正確端點是 `record-info`

### kie.ai API — Veo 3

| 用途 | 端點 |
|------|------|
| 建立任務 | `POST https://api.kie.ai/api/v1/veo/generate` |
| 查詢任務 | `GET https://api.kie.ai/api/v1/veo/record-info?taskId={taskId}` |

- Request body: `{ prompt, generationType: "FIRST_AND_LAST_FRAMES_2_VIDEO", imageUrls: [imageUrl], model: "veo3_lite", aspect_ratio: "9:16" }`
- **沒有 `state` 欄位**，改用 `data.successFlag === 1` 判斷完成
- 影片網址在 `data.response` 物件內（欄位名稱待確認）
- **注意：** `REFERENCE_2_VIDEO` 只支援 `veo3_fast`，`veo3_lite` 要用 `FIRST_AND_LAST_FRAMES_2_VIDEO`
- **注意：** `/veo/record-detail` 是 404，`recordInfo` 回傳 "recordInfo is null"，正確端點是 `record-info`

### kie.ai API — Grok（未解決）

- 模型名稱：`grok-imagine/extend`
- `createTask` 端點回傳 `500 "This field is required"`，缺少某個必填欄位
- 無公開 API 文件，正確格式未知，目前 UI 保留選項但無法成功建立任務

## Prompt 處理

- LLM 產生的完整腳本（含 SHOT 分場）對 Runway / Veo3 太長
- 程式自動從腳本中提取 `SORA PROMPT:` 段落，截至 800 字元送出
- `extractVideoPrompt(fullPrompt)` 函式負責此邏輯

## 已踩過的坑

- `renderTasks()` 第一次渲染後 `empty-hint` 從 DOM 移除，之後 `getElementById` 回傳 null → 靜默崩潰，改用 innerHTML 重建
- kie.ai 狀態值是 `"waiting"` 不是 `"wait"`；Runway 回傳 `"queueing"`（含 ing）不是 `"queuing"`
- kie.ai 圖片上傳域名是 `kieai.redpandaai.co`（不是 `api.kie.ai`）
- OpenRouter `X-Title` 不可含中文
- Netlify 免費額度 300 credits/月，每次部署約 15 credits，盡量本地測試好再部署
- 輪詢拿到 null state 時不可用 `state || 'wait'` 蓋掉現有狀態，要先檢查 `if (state)` 才更新
- Veo3 / Runway 任務 ID 無法用通用 `recordInfo` 查詢，必須用各自的 `record-info` 端點
