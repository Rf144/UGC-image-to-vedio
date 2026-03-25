# 圖生影片工具

電商 UGC 影片自動化工具。拖曳產品圖片 → AI 分析產生腳本 → kie.ai 生成影片 → 自動下載。

**線上網址：** https://frabjous-peony-72f93f.netlify.app

---

## 功能流程

```
拖曳圖片 → LLM 分析（OpenRouter）→ 產生 UGC Prompt → kie.ai 生成影片 → 下載
```

- 支援批量：送出後表單自動清空，可同時處理多張圖片
- 任務列表：每個任務獨立在背景輪詢，完成後顯示下載按鈕
- API Key 儲存於瀏覽器 localStorage，不寫進程式碼

---

## API

| 項目 | 服務 | 用途 |
|------|------|------|
| OpenRouter | openrouter.ai | 圖片分析、產生 Prompt |
| kie.ai | kie.ai | 影片生成 |

---

## 影片模型與價格

| 模型 | 價格 |
|------|------|
| Sora 2 Stable | ~$0.175 / 10秒 |
| Sora 2 標準 | ~$1 / 10秒 |
| Sora 2 Pro 720p | ~$3 / 10秒 |
| Sora 2 Pro 1080p | ~$5 / 10秒 |
| Veo 3.1 Fast | $0.40 / 支 |
| Veo 3.1 Quality | $2.00 / 支 |
| Kling v2.1 Pro | 查官網 |
| Hailuo 2.3 Pro | 查官網 |
| Wan 2.6 | 查官網 |

---

## 本地開發

```bash
# 部署到 Netlify
netlify deploy --prod --dir .
```
