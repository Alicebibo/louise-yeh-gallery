# Louise Yeh 線上畫展 — 專案說明（給 Claude Code）

畫家 Louise Yeh 的線上個展網站。純靜態網頁，部署在 GitHub Pages，會持續新增作品。
專案負責人是 Alice（GitHub 帳號 Alicebibo），溝通一律使用繁體中文。

- 公開網址：https://alicebibo.github.io/louise-yeh-gallery/
- GitHub：https://github.com/Alicebibo/louise-yeh-gallery（branch `main`，Pages 從 `main` 根目錄部署）

## 專案結構

```
index.html          整個網站（HTML + CSS + JS 都在這一個檔案）
works/              網頁用作品圖（長邊 1800px，JPG 品質約 84）
  NNN-slug.jpg          原作（已加署名）
  NNN-slug-framed.jpg   裱框版（燈箱「裱框」檢視用）
music/              背景音樂（自帶檔案，不外連）
.nojekyll           關閉 GitHub Pages 的 Jekyll 處理
_source/            （不進 Git，見 .gitignore）原始高解析檔與工具
  originals/            每件作品的原始照片、加署名的高解析版、裱框高解析版
  tools/signature/      Louise 的署名筆跡素材與加署名的腳本
  tools/frames/         各種畫框的產生腳本
```

## 作品資料

作品清單寫在 `index.html` 裡的 `<script type="application/json" id="works">`。每件作品一筆：

```json
{
  "no": 3,
  "title": "鐵橋旁的車票小舖",
  "titleEn": "The Ticket Shop by the Bridge",
  "date": "2026.10.15",
  "medium": "水彩、代針筆",
  "mediumEn": "Watercolor and ink on paper",
  "image": "works/003-ticket-shop.jpg",
  "framed": "works/003-ticket-shop-framed.jpg",
  "w": 1800, "h": 1145,
  "note": "作品介紹文字（可空白）"
}
```

- `no` 依序遞增，不重複使用。網頁會自動依 `no` 由大到小排序。
- **`no` 最大的那件會自動成為頁首的「最新作品」區塊**（大圖＋介紹文字＋按鈕），其餘作品排在下方「更多作品」。
- `w`、`h` 填 `works/` 裡原作圖的實際像素，用來避免版面跳動。
- 頁首的作品數、媒材、日期範圍都是從這份清單自動算出來的，不用手改。

## 新增作品的流程

Alice 通常會傳一張畫作照片，有時附上實景照片。

1. **確認資訊**：作品名稱（中英）、日期、媒材。沒給的話，依畫面提出建議並請她確認，不要自己定案就上線。
2. **加署名**：右下角，先寫日期 `YYYY.MM.DD`，下一行寫「Louise」，都用 Louise 本人的筆跡（`_source/tools/signature/`）。
   - 放在畫面中較乾淨、顏色較淺的區域，避免壓到線條或深色塊。
   - 筆跡素材裡只有數字 `0 1 2 6` 和點。缺的數字（例如之前的 5）是仿照筆觸畫出來的，要放大檢查自然度，並跟 Alice 說明。
3. **裱框**：依媒材挑框（都放在 `_source/tools/frames/`）。
   - 鉛筆素描：深胡桃木框＋米白卡紙（`frame.py`），或古典金框（`louvre.py`）。
   - 水彩：淺色原木框＋金色細邊＋寬米白卡紙（`wc_frame.py`／`k_frame.py`）。
   - 大圖記得先縮到長邊約 2000px 再處理，否則記憶體會不夠。
4. **輸出網頁用圖**：原作與裱框版各一張，長邊 1800px、JPG 品質約 84，命名 `works/NNN-slug.jpg`、`works/NNN-slug-framed.jpg`。高解析版放 `_source/originals/`。
5. **更新 `index.html`** 的作品 JSON，新增一筆。必要時同步更新 `<head>` 的 `og:description`、`og:image`（社群分享預覽用最新作品）。
6. **本機預覽**後再推上 GitHub（見下方部署）。

## 背景音樂

- 曲目：Erik Satie〈Gymnopédie No.1〉，Robin Alciatore 演奏，來源 Musopen，在 Wikimedia Commons 標示為公眾領域。
- 檔案：`music/satie-gymnopedie-1.mp3`（Wikimedia 連結保留為第二個備援 `<source>`）。
- 行為：預設嘗試自動播放，從 0 淡入到約 35% 音量。瀏覽器擋自動播放時，右下角按鈕顯示「點一下開啟音樂」，訪客第一次點擊／觸控／按鍵就開始播放。訪客按「暫停音樂」後會記在 localStorage（`bgm-off`），下次不再自動播放。
- 頁尾有音樂出處說明，換曲時要一併更新。換曲只能用公眾領域或明確允許的授權錄音。

## 部署

- 推到 `main` 就會自動更新 GitHub Pages，通常 1–2 分鐘生效，瀏覽器用 Ctrl + F5 強制重新整理。
- 推送時使用 Alice 電腦上的 Git 登入（Windows 的 Git Credential Manager 會跳出 GitHub 登入視窗）。
- **不要把任何權限碼（`github_pat_…`）寫進檔案、commit 或指令紀錄。**

## 設計慣例

- 配色、字型都定義在 `:root` 的 CSS 變數，支援深色與淺色模式。新增元件請沿用變數，不要寫死顏色。
- 字型：Bodoni Moda（畫家名、英文標題）、Noto Serif TC（中文標題）、Noto Sans TC（內文）、IBM Plex Mono（編號、標籤）。
- 主要按鈕：古銅金底（`--accent`）配 `--on-accent` 文字。
- 點畫作開燈箱，可切換「原作／裱框」，支援方向鍵與左右滑動。
- 網頁文案語氣平實溫和，不誇大；作品介紹只描述畫面上看得到的內容。
