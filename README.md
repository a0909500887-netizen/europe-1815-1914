# Europe, 1815–1914｜從維也納會議到第一次世界大戰

**一張可以拖拉調整國界、可切換年代的互動歷史地圖**，把「歐洲協調制度」的成立、運作與崩解放在同一條時間軸上：1815 維也納會議 → 1871 德意志統一 → 1914 塞拉耶佛。

> 線上版：`https://<你的帳號>.github.io/<repo 名稱>/`
> 單一 HTML 檔、完全離線可用、不需安裝任何軟體。

![1815 維也納會議](screenshots/1815_vienna.png)

## 這是什麼

以 1815／1878／1914 等歷史底圖為骨架、再用現代海岸線與行政區界校正的歐洲政治地圖，支援：

- **21 個時代斷點**：1815、1830、1848、1853、1856、1859、1860、1861、1864、1866、1867、1870、1871、1878、1881、1885、1905、1908、1912、1913、1914
- **50 個政治實體、61 個地塊**：英、法、普魯士、奧地利（1867 後奧匈）、俄羅斯、鄂圖曼、德意志與義大利諸邦，塞爾維亞、羅馬尼亞、保加利亞、希臘、阿爾巴尼亞、蒙特內哥羅，以及聯合尼德蘭王國、瑞典、挪威等
- **英文原文國名**（採當時用法），中文為副標；部分國名隨年代變化（例：`MOLDAVIA & WALLACHIA` → `ROMANIA`）
- **40 個關鍵事件**：點選即在側欄顯示起因、經過、條約條款與對「歐洲協調」的意義
- **領土可調整**：`Edit borders` 直接拖曳國界；或在「編輯課程內容 → 領土」分頁改「某地塊從哪一年起屬於誰」
- **匯出**：PNG（講義／PPT）與 JSON（保存你調整後的疆域）

## 怎麼用

1. 直接開線上版網址。
2. 或下載 `index.html` 雙擊開啟（完全離線，適合教室沒網路）。
3. 操作、五個課堂活動與編輯方式見 [`使用說明.md`](使用說明.md) 與 [`編輯指南.md`](編輯指南.md)。

### 深連結（可直接寫進講義）

```
…/index.html#y=1871
…/index.html#y=1878&k=2.6&clon=22&clat=42.5
…/index.html#y=1866&sel=prussia
…/index.html#selftest=1        ← 25 項自我測試
```

## 更多畫面

| 1878 柏林會議後的巴爾幹 | 1914 開戰前 |
|---|---|
| ![1878](screenshots/1878_berlin_balkans.png) | ![1914](screenshots/1914_outbreak.png) |

| 1866 普奧戰爭後 | 編輯課程內容 |
|---|---|
| ![1866](screenshots/1866_germany.png) | ![編輯器](screenshots/editor_territory.png) |

## 持續編輯（像維基一樣）

內容與幾何分離：**改 `content.json` 就會讓網站內容跟著變**，不需要重新編譯；每次 commit 都可比較、可回退。

- **網頁內**：地圖左欄 **「✎ 編輯課程內容」** → 事件／國家／領土／年代／匯入匯出 → 「下載 content.json」→ 上傳覆蓋。
- **GitHub 上**：點 `content.json` → 鉛筆 **Edit** → **Commit changes**。
- **交給 AI**：`content.json` + [`docs-ai/給AI的專案說明.md`](docs-ai/給AI的專案說明.md) 丟給 Claude／ChatGPT，用 `docs-ai/prompts/` 的範本下指令，AI 回傳完整 `content.json`。

| 可改 | 不可改（需重新建置） |
|---|---|
| 事件、國名與說明、地圖英文標籤 | 國界線的實際形狀、把一國切兩半 |
| **某地塊某年之後歸屬誰**、**年代斷點** | 新增國家或地塊 |
| 城市／戰役標記、海域、地塊註記 | 配色、投影、版面 |

## 資料來源與授權

- **歷史疆域骨架**：[historical-basemaps](https://github.com/aourednik/historical-basemaps)（André Ourednik，**CC BY-SA 4.0**）——1815／1878／1900／1914 世界歷史地圖
- **海岸線與行政區界**：[Natural Earth](https://www.naturalearthdata.com/) 1:10m（**public domain**）
- 原歷史圖層在比薩拉比亞、高加索等處有誤，已於本專案修正

因衍生自 CC BY-SA 4.0 資料，**本 repo 以 [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.zh_TW) 釋出**：可自由使用、改作、教學與商業利用，但須保留姓名標示，且改作後同樣以 CC BY-SA 4.0 釋出。

## 檔案

```
index.html                地圖本體（程式 + 疆域幾何 + 內建內容備援）
content.json              ★ 事件／國名／領土歸屬／年代斷點（改了就會生效）
使用說明.md                操作說明
編輯指南.md                持續編輯與協作流程
docs-ai/                  給 AI 的專案說明與 8 個指令範本
screenshots/              README 用截圖
.nojekyll                 告訴 GitHub Pages 不要跑 Jekyll
```

## 更新方式

程式改版時才需要換 `index.html`；日常調整教材只要改 `content.json`。
Pages 約 1–2 分鐘後自動更新；若同網址看到舊版，按 `Ctrl + F5`。

## 致謝

- André Ourednik — historical-basemaps
- Natural Earth — 地理資料
- 介面與地圖引擎：本專案自製（SVG + Lambert 等角圓錐投影）
