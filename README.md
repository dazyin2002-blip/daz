# 永續人森 — 部署用資料夾

一款以真實統計與數據推估建構的永續職涯人生模擬器。
單一 HTML 檔、無後端、無建置流程，直接丟到任何靜態網站服務就能跑。

```
site/
├── index.html   ← 遊戲本體（自包含，含所有資料與邏輯）
├── og.png       ← 社群分享預覽圖 1200×630
└── README.md
```

> ⚠️ **這個資料夾是唯一會上網的地方。**
> 上層的 `data/` 含有社群對話（真實姓名與 email）、他人著作的逐字稿與報告，
> **永遠不要**把它複製進來或加進這個 repo。

---

## 每次更新的流程

改完上層的 `netzero-life.html` 之後：

```bash
python build-site.py
```

這支腳本會把工作檔包成完整的 HTML 文件（補上 doctype、charset、viewport、
社群分享 meta、內嵌 favicon），輸出到 `site/index.html`。

接著推上去，Cloudflare 會自動重新部署：

```bash
cd site && git add -A && git commit -m "更新遊戲內容" && git push
```

推完約 30 秒後網站就更新了。

---

## 首次設定 Cloudflare Pages

### 1. 先把這個資料夾推到 GitHub

在 GitHub 建一個新的 repo（**建議設為 Public**，Cloudflare 免費方案連 Private 也支援，
但 Public 之後比較好找協作者），然後：

```bash
cd site
git remote add origin https://github.com/你的帳號/你的repo名.git
git branch -M main
git push -u origin main
```

### 2. 連到 Cloudflare Pages

1. 到 [dash.cloudflare.com](https://dash.cloudflare.com) 註冊／登入
2. 左側選 **Workers & Pages** → **Create** → **Pages** → **Connect to Git**
3. 授權 GitHub，選剛才那個 repo
4. 建置設定全部**留空**（這是純靜態檔，不需要建置）：
   - Framework preset：`None`
   - Build command：**留空**
   - Build output directory：**留空**（或填 `/`）
5. 按 **Save and Deploy**

約一分鐘後會拿到一個 `你的專案名.pages.dev` 網址。

### 3. 回頭改網址設定

拿到正式網址後，編輯上層的 `build-site.py`，把最上方的

```python
SITE_URL = "https://sustalent.pages.dev"
```

改成你實際的網址，然後重新 `python build-site.py` 並推一次。
**這一步不能省** — 社群分享的預覽圖是靠絕對網址抓的，網址不對就不會顯示。

### 4.（選配）掛自己的網域

Cloudflare Pages 專案頁 → **Custom domains** → **Set up a domain**。
免費、自動 HTTPS。網域本身要另外買（`.com` 一年約 NT$400 上下）。

---

## 為什麼選 Cloudflare Pages

- **頻寬不計量** — 免費方案沒有流量上限。同類型的台灣網頁遊戲爆紅時流量很可觀，
  GitHub Pages 每月 100GB 的軟上限有被限速的風險
- 全球 CDN，台灣使用者連線快
- 免費自訂網域與自動 HTTPS
- 每次 push 都保留一個預覽版本，改壞了可以立刻回滾

---

## 分享時的檢查清單

上線後貼連結到 Threads／LINE／Facebook 前，先用
[opengraph.xyz](https://www.opengraph.xyz/) 或
[Facebook 分享偵錯工具](https://developers.facebook.com/tools/debug/)
貼上網址檢查預覽圖有沒有正常出現。

若改過 `og.png` 但社群平台還顯示舊圖，是平台快取的關係，
用上面的偵錯工具按一次 **Scrape Again** 就會更新。
