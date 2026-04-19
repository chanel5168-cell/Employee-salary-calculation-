# LINE LIFF 部署教學

把 `index.html` 部署到 HTTPS 網站並掛進 LINE 的完整步驟。

## 選項一：GitHub Pages (免費、推薦)

**步驟 1. 建立 GitHub 帳號與倉庫**

前往 https://github.com 註冊，點 `New repository` 建立一個倉庫（名稱例如 `salary-app`），設為 `Public`。

**步驟 2. 上傳 HTML**

直接將此資料夾內的 `index.html` 拖入倉庫頁面上傳並 commit。

**步驟 3. 啟用 Pages**

進入倉庫 `Settings` → `Pages` → `Source` 選 `main` 分支 → `Save`。等約一分鐘即可獲得網址：
```
https://你的帳號.github.io/salary-app/
```

## 選項二：Netlify (免費、更簡單)

前往 https://app.netlify.com，登入後將整個資料夾直接拖入頁面，幾秒即可獲得 `https://xxx.netlify.app` 網址。

## 選項三：Vercel

前往 https://vercel.com，連接 GitHub 或直接上傳檔案，自動取得 HTTPS 網址。

---

## 建立 LINE LIFF App

**步驟 1. LINE Developers 後台**

前往 https://developers.line.biz，用您的 LINE 帳號登入。

**步驟 2. 建立 Provider**

點 `Create a new provider`，輸入名稱（例如公司名）。

**步驟 3. 建立 Channel**

在 Provider 下建立 `LINE Login` Channel：
- Channel name: 薪資系統
- Channel description: 員工薪資計算
- App types: 勾選 `Web app`

**步驟 4. 新增 LIFF App**

進入 Channel → LIFF 分頁 → `Add`：
- LIFF app name: 薪資系統
- Size: `Full`
- Endpoint URL: 貼上您部署的 HTTPS 網址（例如 `https://xxx.github.io/salary-app/`）
- Scope: 勾選 `profile`、`openid`
- Scan QR: 勾選 `Enable`

建立後會取得 **LIFF ID**，格式為 `1234567890-AbCdEfGh`。

**步驟 5. 網頁內設定 LIFF ID**

開啟您部署的網頁 → 點「設定」頁籤 → 將 LIFF ID 貼入 → 按「儲存並重新初始化」。

**步驟 6. 在 LINE 分享**

LIFF URL 為：
```
https://liff.line.me/你的LIFF_ID
```

將此網址貼到 LINE 群組或個人訊息，點擊即可在 LINE 內開啟網頁。

---

## 常見問題

**Q: 不同人看到的資料會一樣嗎？**
目前資料儲存在每個使用者自己的瀏覽器本機 (localStorage)，因此每個裝置的資料是獨立的。如需共用，需改為連接雲端資料庫（Firebase / Supabase 等）。

**Q: 如何讓特定員工才能使用？**
可在 LIFF 設定中加上 `Bot link` 綁定官方帳號，或於程式內加上 LINE 使用者 ID 白名單（需另行開發）。

**Q: 我可以免費用多久？**
LINE LIFF、GitHub Pages、Netlify、Vercel 都是永久免費（有用量上限但小公司用不到）。
