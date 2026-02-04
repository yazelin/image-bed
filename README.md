# Image Bed - 個人圖床

純前端個人圖床，圖片存放在 GitHub Releases，不佔用 repo 空間。

## 特色

- **不佔 repo 空間** - 圖片存在 Release assets
- **純前端** - 不需要 Cloudflare Worker 或任何後端
- **單檔最大 2GB** - Release asset 限制
- **樹狀資料夾** - 支援資料夾分類
- **一鍵複製** - 複製連結或 Markdown 語法
- **完全免費** - GitHub Pages + Releases

## 使用方式

### 1. 使用此 Template

點擊 "Use this template" 建立你自己的 repo

### 2. 啟用 GitHub Pages

1. 到 repo 的 Settings → Pages
2. Source 選擇 `main` branch
3. 等待部署完成

### 3. 建立 Personal Access Token

1. 前往 [GitHub Token 設定](https://github.com/settings/tokens/new?scopes=repo&description=image-bed)
2. 勾選 `repo` 權限
3. 建立並複製 Token

### 4. 開始使用

1. 開啟你的 GitHub Pages 網址 (例如: `https://username.github.io/image-bed`)
2. 點擊「設定」輸入 Token 和 repo 名稱
3. 開始上傳圖片！

## 檔案結構

```
image-bed/
├── index.html      # 上傳 + 瀏覽介面
├── index.json      # 圖片索引（自動更新）
├── README.md
└── releases/
    └── images/     # (GitHub Release tag，存放圖片)
```

## 技術細節

### 圖片網址格式

```
https://github.com/{owner}/{repo}/releases/download/images/{id}_{filename}
```

### index.json 格式

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

## 限制

| 項目 | 限制 |
|------|------|
| 單檔大小 | 2 GB |
| Release 總大小 | 無硬性限制 |
| API 請求 | 5000 次/小時 |

## 安全性

- Token 只存在瀏覽器的 localStorage
- 只有知道 Token 的人才能上傳/刪除
- 圖片連結是公開的（GitHub Releases 本身是公開的）

## License

MIT
