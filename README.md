# 圖生影片工具

一個網頁工具，將電商產品圖片自動轉換為 UGC 風格影片。

## 功能流程

```
拖曳圖片 → LLM 分析圖片 → 產生 UGC Prompt → Kling AI 生成影片 → 自動下載
```

## 功能說明

### 1. 圖片輸入
- 支援拖曳上傳圖片
- 支援點擊選擇檔案

### 2. LLM 分析（OpenRouter API）
- 下拉選單選擇模型（Gemini、GPT-4o 等）
- 可自訂 System Prompt
- 分析圖片內容，產生電商 UGC 影片 Prompt

### 3. 影片生成（Kling AI API）
- 將圖片 + Prompt 送出
- 等待影片生成完成
- 自動下載影片

## 技術架構

- 前端：純 HTML / CSS / JavaScript（單一 .html 檔案）
- LLM API：OpenRouter（支援 Gemini、GPT-4o 等多模型）
- 影片 API：Kling AI

## API 設定

| 項目 | 說明 |
|------|------|
| OpenRouter API Key | 用於 LLM 圖片分析 |
| Kling AI API Key | 用於影片生成 |

## 使用方式

1. 開啟 `index.html`
2. 填入 API Key
3. 選擇 LLM 模型
4. （選填）修改 System Prompt
5. 拖曳產品圖片進入畫面
6. 等待 LLM 分析並確認 Prompt
7. 送出生成影片，完成後自動下載
