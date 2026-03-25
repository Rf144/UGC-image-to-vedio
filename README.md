# 圖生影片工具

電商 UGC 影片自動化工具。拖曳產品圖片 → AI 分析產生腳本 → kie.ai 生成影片 → 自動下載。

**線上網址：** https://frabjous-peony-72f93f.netlify.app
**GitHub：** https://github.com/Rf144/-
**本地開發：** `http://localhost:5500`（需啟動本地伺服器）

---

## 功能清單

| 功能 | 說明 |
|------|------|
| 批量操作 | 送出後表單自動清空，可同時處理多張圖片 |
| 任務列表 | 每個任務獨立在背景輪詢（最長 20 分鐘） |
| 計時器 | 每個進行中任務顯示已等待時間 |
| 任務持久化 | localStorage 儲存，刷新頁面或換裝置仍保留，自動恢復輪詢 |
| 失敗重試 | 失敗任務一鍵重試（不需重新上傳圖片） |
| 智慧檔名 | 下載時 LLM 從 Prompt 提取產品關鍵字作為檔名（20-30字） |
| API Key 安全 | 儲存於瀏覽器 localStorage，不寫進程式碼、不上傳 |

---

## 操作流程

```
上傳圖片 → 分析圖片產生 Prompt → 選影片模型 → 加入佇列 → 等待完成 → 下載影片
```

- 第一張加入佇列後，**不需等待**，可立刻上傳下一張
- Prompt 送出後保留，新圖上傳即可直接加入佇列（不必重新分析）

---

## API 設定

| 項目 | 服務 | 用途 |
|------|------|------|
| OpenRouter API Key | openrouter.ai | 圖片分析、產生 Prompt、提取關鍵字檔名 |
| kie.ai API Key | kie.ai | 影片生成 |

### kie.ai API 端點

| 用途 | 端點 |
|------|------|
| 圖片上傳 | `POST https://kieai.redpandaai.co/api/file-base64-upload` |
| 建立任務 | `POST https://api.kie.ai/api/v1/jobs/createTask` |
| 查詢任務 | `GET https://api.kie.ai/api/v1/jobs/recordInfo?taskId={taskId}` |

- 狀態欄位：`data.state`（值：`waiting` / `queuing` / `generating` / `success` / `fail`）
- 影片網址：解析 `data.resultJson` → `resultUrls[0]`
- 媒體保留期限：14 天

---

## 影片模型與價格

| 模型 | model 字串 | 價格 |
|------|-----------|------|
| Sora 2 Stable | sora-2-image-to-video-stable | ~$0.175 / 10秒 |
| Sora 2 標準 | sora-2-image-to-video | ~$1 / 10秒 |
| Sora 2 Pro 720p | sora-2-pro-image-to-video (size:standard) | ~$3 / 10秒 |
| Sora 2 Pro 1080p | sora-2-pro-image-to-video (size:high) | ~$5 / 10秒 |
| Veo 3.1 Fast | veo-3-1-fast | $0.40 / 支 |
| Veo 3.1 Quality | veo-3-1-quality | $2.00 / 支 |
| Kling v2.1 Pro | kling/v2-1-pro | 查官網 |
| Hailuo 2.3 Pro | hailuo/2-3-image-to-video-pro | 查官網 |
| Wan 2.6 | wan/2-6-image-to-video | 查官網 |

---

## 本地開發

```bash
# 1. 啟動本地伺服器（擇一）
export PATH="/c/Users/User/AppData/Roaming/npm:/c/Program Files/nodejs:$PATH"
cd "c:/Users/User/Desktop/VS Code"
npx serve -p 5500          # 用 Node.js（推薦）

# 2. 開啟瀏覽器
# http://localhost:5500

# 3. 部署到 Netlify（測試完再部署，每次約消耗 15 credits，免費額度 300/月）
git add index.html && git commit -m "更新" && git push
netlify deploy --prod --dir .
```

> **注意：** 不可直接用瀏覽器開啟 HTML 檔案（file:// 會被 CORS 封鎖 API 呼叫）

---

## 已解決的技術問題

- `renderTasks()` null 崩潰：`empty-hint` 移除後 `getElementById` 回傳 null → 改用 innerHTML 重建
- 兩個 `submitTask` 衝突：合併為一個，先儲存資料再清空表單
- kie.ai 狀態值：實際回傳 `"waiting"` 不是 `"wait"`
- kie.ai 圖片上傳域名：`kieai.redpandaai.co`（不是 `api.kie.ai`）
- OpenRouter header 含中文報錯：`X-Title` 改為 `'UGC Video Tool'`
- API Key 被 GitHub 自動撤銷：改用 localStorage 儲存
