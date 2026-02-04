# Image Bed - 個人圖床

免費個人圖床，圖片存放在 GitHub Releases，**不佔用 repo 空間**。

## 特色

- **不佔 repo 空間** - 圖片存在 GitHub Release assets，不會讓 repo 變肥
- **完全免費** - GitHub + Cloudflare Workers 免費額度超級夠用
- **有 CDN** - 透過 GitHub 全球節點分發
- **樹狀資料夾** - 支援資料夾分類管理
- **一鍵複製** - 快速複製圖片連結或 Markdown 語法
- **拖放上傳** - 直接拖放圖片到網頁上傳

## 架構說明

```
┌─────────────────────────────────────────────────────────────────┐
│                        你的瀏覽器                                │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  GitHub Pages (yazelin.github.io/image-bed)             │   │
│  │  - 上傳介面                                              │   │
│  │  - 瀏覽介面                                              │   │
│  │  - Token 存在 localStorage                              │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
         │                                    │
         │ 上傳圖片                            │ 讀取 index.json
         ▼                                    ▼
┌─────────────────────┐              ┌─────────────────────┐
│  Cloudflare Worker  │              │  GitHub Repo        │
│  (上傳代理)          │              │  - index.json       │
│                     │              │    (圖片清單，幾KB)  │
│  為什麼需要？        │              └─────────────────────┘
│  GitHub Release     │
│  上傳 API 不支援     │
│  瀏覽器直接呼叫      │
│  (CORS 限制)        │
└─────────────────────┘
         │
         │ 代理上傳
         ▼
┌─────────────────────────────────────────────────────────────────┐
│  GitHub Release Assets                                          │
│  - 圖片實際儲存位置                                              │
│  - 不佔 repo 空間                                                │
│  - 單檔最大 2GB                                                  │
│  - 網址: github.com/{user}/{repo}/releases/download/images/...  │
└─────────────────────────────────────────────────────────────────┘
```

### 為什麼需要 Cloudflare Worker？

GitHub 的 Release 上傳 API (`uploads.github.com`) 不支援瀏覽器直接呼叫（CORS 限制）。
所以需要一個中間代理來轉發上傳請求。

**Worker 做的事很簡單：**
1. 接收你瀏覽器發來的圖片
2. 轉發給 GitHub Release API
3. 回傳結果

**安全性：**
- Worker 只允許來自 `yazelin.github.io` 的請求
- Worker 只允許上傳到 `yazelin/image-bed` repo
- 你的 Token 不會被 Worker 儲存

## 使用方式

### 方式一：直接使用（推薦）

如果你是 `yazelin`，直接開啟：

**https://yazelin.github.io/image-bed/**

1. 點右上角 ⚙️ 設定
2. 輸入你的 [GitHub Token](https://github.com/settings/tokens/new?scopes=repo&description=image-bed)（需要 `repo` 權限）
3. Repository 填 `yazelin/image-bed`
4. 儲存後就能上傳了！

### 方式二：自己架設

如果你想架設自己的圖床：

#### 1. Fork 這個 repo

點擊右上角 Fork

#### 2. 啟用 GitHub Pages

Settings → Pages → Source 選 `main` branch

#### 3. 部署自己的 Worker

你需要部署一個 Cloudflare Worker 作為上傳代理：

```javascript
// 參考 https://github.com/yazelin/shorturl-worker
// 或直接 fork 那個 repo
```

修改 Worker 中的 `ALLOWED_IMAGE_REPOS` 為你的 repo。

#### 4. 修改前端設定

編輯 `index.html`，修改 `CONFIG.UPLOAD_PROXY` 為你的 Worker URL。

## 檔案結構

```
image-bed/
├── index.html      # 上傳 + 瀏覽介面（單一檔案，約 1300 行）
├── index.json      # 圖片索引（自動更新，只有幾 KB）
└── README.md       # 本文件

# GitHub Release (不在 repo 裡，不佔空間)
releases/
└── images/         # Release tag
    ├── abc123_photo1.jpg
    ├── def456_cat.png
    └── ...
```

## index.json 格式

```json
{
  "images": [
    {
      "id": "abc123",
      "filename": "photo.jpg",
      "folder": "Blog/Tech",
      "size": 123456,
      "uploadedAt": "2024-01-15T10:30:00Z"
    }
  ]
}
```

## 圖片網址格式

```
https://github.com/{owner}/{repo}/releases/download/images/{id}_{filename}
```

例如：
```
https://github.com/yazelin/image-bed/releases/download/images/abc123_photo.jpg
```

## 限制

| 項目 | 限制 | 說明 |
|------|------|------|
| 單檔大小 | 2 GB | GitHub Release asset 限制 |
| 總容量 | 無硬性限制 | Release 不佔 repo 空間 |
| 上傳頻率 | 10 次/分鐘 | Worker rate limit |
| API 請求 | 5000 次/小時 | GitHub API 限制 |

## 相關專案

- [shorturl-worker](https://github.com/yazelin/shorturl-worker) - 上傳代理 Worker

## 技術部落格

- [使用 Cloudflare Workers 免費架設短網址服務](https://yazelin.github.io/serverless/%E6%95%99%E5%AD%B8/2025/12/29/cloudflare-workers-shorturl.html)

## License

MIT
