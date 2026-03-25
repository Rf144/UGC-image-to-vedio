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

1. 每次修改完若需上線：`git add index.html && git commit -m "..." && git push && netlify deploy --prod --dir .`
2. **不可把 API Key 寫進程式碼**（GitHub 會自動偵測並撤銷）
3. API Key 一律用 localStorage 儲存
4. 本地測試用 `npx serve -p 5500`，不需每次部署 Netlify（免費額度有限）

## 部署指令

```bash
export PATH="/c/Users/User/AppData/Roaming/npm:/c/Program Files/nodejs:$PATH"
cd "c:/Users/User/Desktop/VS Code"
git add index.html && git commit -m "說明" && git push
netlify deploy --prod --dir .
```

## 技術架構

- 單一 `index.html`（含所有 CSS + JavaScript）
- OpenRouter API：圖片分析、產生 Prompt、提取關鍵字
- kie.ai API：影片生成

### kie.ai API

| 用途 | 端點 |
|------|------|
| 圖片上傳 | `POST https://kieai.redpandaai.co/api/file-base64-upload` |
| 建立任務 | `POST https://api.kie.ai/api/v1/jobs/createTask` |
| 查詢任務 | `GET https://api.kie.ai/api/v1/jobs/recordInfo?taskId={taskId}` |

- 狀態欄位：`data.state`（值：`waiting` / `queuing` / `generating` / `success` / `fail`）
- 影片網址：解析 `data.resultJson` → `resultUrls[0]`

## 已踩過的坑

- `renderTasks()` 第一次渲染後 `empty-hint` 從 DOM 移除，之後 `getElementById` 回傳 null → 靜默崩潰，改用 innerHTML 重建
- kie.ai 狀態值是 `"waiting"` 不是 `"wait"`
- kie.ai 圖片上傳域名是 `kieai.redpandaai.co`（不是 `api.kie.ai`）
- OpenRouter `X-Title` 不可含中文
- Netlify 免費額度 300 credits/月，每次部署約 15 credits，盡量本地測試好再部署
