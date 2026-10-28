# 給 AI 的專案說明：歐洲 1853–1871 互動歷史地圖

> **這份文件是寫給「要幫老師改教材的 AI」看的專案簡報。**
> 老師本人只要看第 0 節、第 1 節，以及每個指令範本最後的「老師收到結果後怎麼做」。
> 把這份文件連同 `content.json` 一起交給 AI；AI 讀完就能在**不看程式碼**的情況下安全修改內容。

## 0. 老師 30 秒版

1. **要改的檔案只有一個：`content.json`**（JSON，一種純文字資料格式，用大括號和引號描述資料；記事本、VS Code 都能開）。
2. `index.html` 是程式與國界幾何，**不要動**；動了整張地圖可能打不開。
3. 最安全流程：`content.json` ＋ 這份文件 ＋ `docs-ai/prompts/` 裡對應的範本 → 丟給 AI → 拿回**完整**的 `content.json` → 先備份舊檔 → 上傳 GitHub（一個存放檔案並能免費架站的網站）覆蓋。
4. AI 若說要「改 `index.html`」「改 `data.js`」「新增國家」「拖動國界」，請直接拒絕，並告訴它「國界幾何不能只靠 JSON 改」。

## 1. 推薦哪個 AI

版本更新很快，以下以 2026 年常見方案為準；**沒有「最好」，只有「最適合這個任務」**。

| AI（常見版本） | 最適合 | 較不適合 | 用法小訣竅 |
|---|---|---|---|
| **Claude**（Sonnet 4.5／Opus 系列） | 處理長篇結構化檔案最穩：一次讀完整份 `content.json` 後做結構性改寫、整批統一格式、檢查內部矛盾 | 需要即時查證最新史學爭議時仍得自己核對；免費額度較少 | 直接**上傳檔案**而不是貼文字；明確要求「輸出完整檔案」；大改用 Opus、日常小改用 Sonnet |
| **ChatGPT**（GPT-5／4o） | 檔案上傳與來回修改最方便；順便整理成表格、講義，或畫時間軸示意圖 | 8 萬字元以上的檔案一次全量重寫，偶爾會漏段或被截斷 | 用「檔案／資料分析」模式上傳；先叫它「列出你改了哪幾處」，再要完整檔 |
| **Gemini**（2.5 Pro 等長上下文版本） | 長上下文：一次塞進整包教材（`content.json` ＋多份說明）做交叉比對、找出前後矛盾 | 台灣中文用語的細膩度較不穩；對 JSON 細節的服從度中等 | 明確要求「只輸出 JSON，不要加 Markdown 說明」；先叫它複述規格，確認讀懂 |
| **DeepSeek**（V3／R1） | 大量中文改寫、口吻統一、便宜快速；R1 適合先做「檢查與推理」 | 超長檔案的輸出完整度較弱；有時會自行簡化沒被要求的欄位 | 一次只改一個主題（例如一輪只改 5 個事件），改完立刻用第 10 節檢查清單驗證 |

**怎麼選（一分鐘版）**

- 要**一次大改結構**（例如新增 10 個事件、整批改寫說明）→ Claude。
- 要**上傳檔案、邊改邊討論、順便產出講義素材** → ChatGPT。
- 要**把整包教材讀完抓矛盾** → Gemini。
- 要**省錢、大量改中文口吻** → DeepSeek，但驗證要更勤。

> ⚠️ 任何 AI 都會犯錯：可能漏掉陣列最後幾筆、把 `"` 和 `”` 搞混、或憑空發明 id。**最後一定要跑第 10 節的檢查**。

## 2. 專案簡介（AI 需要知道的背景）

**一句話**：一張可以切換年代的歐洲政治地圖，涵蓋 1853 年克里米亞戰爭到 1871 年德意志帝國成立，給大學「近代外交史／歐洲國際關係史」課程使用。

**規模（內容都在 `content.json`）**

| 項目 | 數量 | 說明 |
|---|---|---|
| 年代斷點 `eras` | 10 個 | 1853、1856、1859、1860、1861、1864、1866、1867、1870、1871；滑桿只在這 10 年跳變 |
| 政治實體 `entities` | 45 個 | 國家、邦國、帝國、邦聯（例：普魯士、巴伐利亞、北德意志邦聯） |
| 地塊 `regions`／`owners` | 49 塊 | 地圖上一塊有顏色的區域；比國家細（例：倫巴底、亞爾薩斯-洛林、南比薩拉比亞） |
| 事件 `events` | 27 個 | 左欄清單；點選在右欄顯示摘要與詳細說明 |
| 城市與戰役 `cities` | 58 個 | `city` 城市、`battle` 戰役、`siege` 圍城 |
| 海域 `seas` | 8 個 | 地圖上的海域英文名 |
| 政治框架 `frames` | 2 個 | 北德意志邦聯（`ngc`）、德意志帝國（`germany`） |

**兩層架構（最重要的一句話）**

```
index.html    ← 程式 + 樣式 + 疆域幾何（49 個地塊的邊界座標）→ 老師不要碰
content.json  ← ★ 所有文字與領土歸屬（事件、國名、說明、哪塊地在哪一年屬於誰）→ 改這個
```

用網址開啟時，網頁會去讀同一資料夾的 `content.json`；讀不到就退回 `index.html` 內建的備援內容（所以你改壞 JSON 時，看到的會是**舊內容**，不是空白頁）。

**可以改／不可以改（一句話）**：文字（事件、國名、說明、年代標籤、城市、海域、地塊註記）與**領土歸屬**都可以改；**國界形狀、配色、新增國家或地塊、新增政治框架**不行，那些固定在幾何資料裡（詳見第 3、11 節）。

## 3. 檔案地圖：哪個檔可以改

| 檔案／資料夾 | 角色 | 可以改嗎 |
|---|---|---|
| `content.json` | ★ 所有文字與領土歸屬，網頁實際讀取的那份 | ✅ **你的工作範圍** |
| `index.html` | 程式、樣式、疆域幾何全部內嵌在裡面 | ❌ 絕對不要改 |
| `README.md` | GitHub 首頁的專案介紹 | ⚠️ 可改，但與教學無關 |
| `使用說明.md`／`.docx` | 給老師的操作手冊 | ⚠️ 可改，非必要 |
| `編輯指南.md` | 用網頁編輯器與 GitHub 改內容的說明 | 可讀，不必改 |
| `screenshots/` | README 用的截圖 | ❌ |
| `docs-ai/` | 這份文件與 `prompts/` 指令範本 | 可讀 |
| `src/`、`tools/`、`build.py`、`*.geojson` | 開發原始碼、資料管線、地圖原始資料（約 60 MB） | ❌ 絕對不要碰 |

**發布方式**：GitHub Pages（把 GitHub 上的專案資料夾變成免費靜態網站的服務）只會用到 `index.html` 與 `content.json`。**上傳覆蓋 `content.json` 就會生效，不需要重新編譯**，約 1–2 分鐘後更新。

## 4. 動手前：JSON 五個基本常識

JSON（一種用大括號和引號描述資料的純文字格式，多看幾次就會）— 只要記住五件事：

| 規則 | 正確 | 錯誤 |
|---|---|---|
| 資料裝在 `{ }`（物件）或 `[ ]`（陣列）裡 | `"eras": [1853, 1856]` | `"eras": (1853, 1856)` |
| 名稱與文字一律用**半形雙引號** `"` | `"zh": "普魯士"` | `"zh": “普魯士”`、`"zh": '普魯士'` |
| 每組之間用逗號，**最後一組後面不能有逗號** | `[1853, 1856]` | `[1853, 1856,]` |
| 巢狀：物件裡放陣列、陣列裡放物件都可以 | `"owners": {"venetia": [[1853,"austria"]]}` | — |
| 不能加註解，也不能放 HTML 標籤（HTML 是網頁用的標記語言，例如 `<b>` 會讓字變粗） | `"titleZh": "克里米亞戰爭"` | `"titleZh": "<b>克里米亞戰爭</b>"` |

**存檔**：編碼選 **UTF-8**（一種通用文字編碼，中文才不會變成亂碼），副檔名一定是 `.json`；不要用 Word 存（會變成 `.docx`），也不要用記事本的「WordPad 格式」。

## 5. `content.json` 全貌

13 個最上層欄位（順序不重要，但**一個都不能少**）。下面只是結構示意，`//` 是給人看的說明，**實際檔案不能有註解**：

```text
{
  "schema": 1,
  "note": "版本備註",
  "eras": [1853, ...],
  "eraLabels": ["Crimean War begins", ...],
  "labels": { "prussia": "PRUSSIA", ... },
  "entities": { "prussia": { ... }, ... },
  "frames": { "ngc": { "en": ..., "zh": ... }, ... },
  "owners": { "venetia": [[1853, "austria"], ...], ... },
  "groups": { "prussia": [[1867, "ngc"], ...], ... },
  "seas": [ { "en": ..., "zh": ..., "lon": ..., "lat": ... }, ... ],
  "cities": [ { "nameEn": ..., ... }, ... ],
  "events": [ { "id": ..., ... }, ... ],
  "regionNotes": { "lombardy": "……", ... }
}
```

## 6. 每個欄位的意思（一句話＋小例子）

表格裡的「key」是物件中每一筆資料的名稱；「id」是給程式看的英文代號（清單見第 7 節）。

| 欄位 | 型別 | 一句話解釋 | 什麼時候會改 |
|---|---|---|---|
| `schema` | 數字 | 資料格式版本，固定是 `1`，**不要改** | 永遠不改 |
| `note` | 文字 | 這份內容的版本備註，可自由寫（例：`2026 秋・近代外交史`） | 每學期換版本時 |
| `eras` | 整數陣列 | 年代斷點，由小到大遞增，時間軸刻度就是它 | 新增／調整年代斷點 |
| `eraLabels` | 文字陣列 | 每個斷點的英文短標，**數量必須等於 `eras`** | 改年代標籤、新增斷點 |
| `labels` | 物件 | 地圖上的英文國名（全大寫），key 是 entity id | 改地圖國名 |
| `entities` | 物件 | 45 個實體的中文名、正式國名、首都、元首、說明 | 改國家資料 |
| `frames` | 物件 | 政治框架（邦聯／帝國）的外框名稱中英各一 | 很少改 |
| `owners` | 物件 | ★ 地塊歸屬時間表：`[[起始年, entity id], …]`，由小到大 | 改某地塊的歸屬 |
| `groups` | 物件 | 地塊屬於哪個政治框架的時間表：`[[起始年, frame id], …]`，可空 | 德意志邦聯→帝國變化 |
| `seas` | 陣列 | 海域名稱與標籤位置（`lon` 經度、`lat` 緯度） | 改海域名／位置 |
| `cities` | 陣列 | 城市與戰役標記（名稱、座標、類型、年份） | 改城市／戰役 |
| `events` | 陣列 | 27 個事件：教學內容的主體 | 改事件、新增事件 |
| `regionNotes` | 物件 | 特定地塊的補充說明（點選該地塊時顯示），key 是 region id | 補地塊說明 |

**小例子（全部取自真實資料）**

```json
"eras": [1853, 1856, 1859, 1860, 1861, 1864, 1866, 1867, 1870, 1871],
"eraLabels": [
  "Crimean War begins", "Treaty of Paris", "War in Italy · Lombardy",
  "Central Italy · Savoy & Nice", "Kingdom of Italy", "Schleswig-Holstein",
  "Austro-Prussian War", "North German Confederation · Ausgleich",
  "Rome · Franco-Prussian War", "German Empire"
],
"labels": { "prussia": "PRUSSIA", "uk": "UNITED KINGDOM", "germany": "GERMAN EMPIRE" }
```

```json
"entities": {
  "prussia": {
    "zh": "普魯士", "formalEn": "Kingdom of Prussia", "formalZh": "普魯士王國",
    "capitalEn": "Berlin", "capitalZh": "柏林",
    "ruler": "Frederick William IV",
    "notes": "1850 年代保守，1862 年俾斯麥任首相並推動軍事改革……"
  }
},
"frames": { "ngc": { "en": "NORTH GERMAN CONFEDERATION", "zh": "北德意志邦聯" } }
```

```json
"owners": {
  "venetia": [[1853, "austria"], [1866, "italy"]],
  "lombardy": [[1853, "austria"], [1859, "sardinia"], [1861, "italy"]],
  "alsace_lorraine": [[1853, "france"], [1871, "germany"]]
},
"groups": {
  "prussia": [[1867, "ngc"], [1871, "germany"]],
  "alsace_lorraine": [[1871, "germany"]]
}
```

```json
"seas": [ { "en": "BLACK SEA", "zh": "黑海", "lon": 34.3, "lat": 43.2 } ],
"cities": [
  { "nameEn": "Sevastopol", "nameZh": "塞瓦斯托波爾",
    "lon": 33.52, "lat": 44.62, "kind": "siege", "year": 1855 }
]
```

```json
"events": [ {
  "id": "crimean_war", "year": 1853, "dateLabel": "1853–1856",
  "titleEn": "The Crimean War", "titleZh": "克里米亞戰爭", "type": "war",
  "summaryZh": "……", "detailZh": "……", "terms": ["Treaty of Paris"]
} ],
"regionNotes": { "lombardy": "1859 年義大利戰爭後，倫巴底由奧地利割予薩丁尼亞……" }
```

**三個容易踩雷的規則（請背下來）**

1. **`owners` 的第一筆建議填 `1853`**（也就是 `eras` 的第一個年份）。程式對「第一筆之前」的年份會套用第一筆的歸屬，若第一筆寫 `1866`，那 1853–1865 也會被算成 `1866` 的國家。
2. **`owners`（以及 `groups`）的年份要落在 `eras` 的範圍內**（1853–1871）。晚於最後一個 `eras` 的轉折永遠不會顯示；早於第一個 `eras` 的轉折則會套用到整個時間軸開頭。
3. **`groups` 的 key 是「地塊（region）」而不是「國家（entity）」**。例如 `prussia` 剛好同名，但它指的是普魯士那塊地；沒有德意志框架的國家（例如法國）不需要出現在 `groups`。

> 附註：實際檔案裡 `events` 另有 `eraLabel`（該事件所屬年代的英文短標）、`cities` 另有 `side`（陣營）兩個附帶欄位。第 6 節表格沒有列出它們，但你改寫檔案時**既有的不要刪**。

## 7. 有效的 id 清單（不可發明）

id（識別碼，給程式看的英文代號）一律是**小寫英文、數字、底線**。下列清單以外的 id **一律視為錯誤**，會導致顏色錯亂或資料被忽略。

### 7-1 entity id（用在 `labels`、`entities`、`owners` 的國家值）

| id | 中文 | id | 中文 |
|---|---|---|---|
| `uk` | 英國 | `france` | 法國 |
| `prussia` | 普魯士 | `austria` | 奧地利 |
| `austria_hungary` | 奧匈帝國 | `russia` | 俄羅斯 |
| `ottoman` | 鄂圖曼帝國 | `bavaria` | 巴伐利亞 |
| `wuerttemberg` | 符騰堡 | `baden` | 巴登 |
| `saxony` | 薩克森 | `hanover` | 漢諾威 |
| `hesse_kassel` | 黑森選侯國 | `hesse_darmstadt` | 黑森大公國 |
| `nassau` | 拿騷 | `brunswick` | 布倫斯維克 |
| `mecklenburg` | 梅克倫堡 | `oldenburg` | 奧爾登堡 |
| `anhalt` | 安哈特 | `thuringia` | 圖林根 |
| `lippe` | 利珀 | `hanse` | 漢薩自由市 |
| `holstein` | 霍爾斯坦 | `schleswig` | 什勒斯維希 |
| `luxembourg` | 盧森堡 | `sardinia` | 薩丁尼亞 |
| `italy` | 義大利 | `tuscany` | 托斯卡尼 |
| `modena` | 摩德納 | `parma` | 帕爾馬 |
| `papal` | 教宗國 | `two_sicilies` | 兩西西里 |
| `greece` | 希臘 | `serbia` | 塞爾維亞 |
| `romania` | 羅馬尼亞 | `montenegro` | 蒙特內哥羅 |
| `denmark` | 丹麥 | `sweden_norway` | 瑞典-挪威 |
| `spain` | 西班牙 | `portugal` | 葡萄牙 |
| `switzerland` | 瑞士 | `belgium` | 比利時 |
| `netherlands` | 荷蘭 | `germany` | 德意志帝國 |
| `ngc` | 北德意志邦聯 | | |

### 7-2 region id（用在 `owners`、`groups`、`regionNotes` 的 key）

- 法國與割讓地：`france`、`alsace_lorraine`、`savoy_nice`
- 德意志：`prussia`、`bavaria`、`wuerttemberg`、`baden`、`saxony`、`hanover`、`hesse_kassel`、`hesse_darmstadt`、`nassau`、`brunswick`、`mecklenburg`、`oldenburg`、`anhalt`、`thuringia`、`lippe`、`hanse`、`holstein`、`schleswig`、`luxembourg`
- 奧地利與義大利：`austria`、`lombardy`、`mantua`、`venetia`、`sardinia`、`tuscany`、`modena`、`parma`、`papal_romagna`、`papal_marche_umbria`、`papal_lazio`、`two_sicilies`
- 俄羅斯與巴爾幹：`russia`、`south_bessarabia`、`greece`、`serbia`、`romania`、`montenegro`、`ottoman`
- 北歐與西歐：`denmark`、`sweden_norway`、`spain`、`portugal`、`switzerland`、`belgium`、`netherlands`、`uk`

> 共 45 個 entity、49 個 region。注意 `papal_romagna`、`papal_marche_umbria`、`papal_lazio` 是三個地塊（教宗國被切成三塊），不是一個 `papal`；`papal` 是 entity id。

### 7-3 frame id 與 event id

- frame id 只有兩個：`ngc`（北德意志邦聯）、`germany`（德意志帝國）。
- event id 是既有事件的固定代號（例：`crimean_war`、`franco_prussian_1870`），**既有事件不要改 id**；新增事件時可以自創 id，規則同上（小寫英文＋底線，例：`danubian_elections_1857`），且不要與現有重複。

## 8. 三條鐵律（違反就會出事）

| 鐵律 | 內容 | 違反的後果 |
|---|---|---|
| **① 只改 JSON** | 只動 `content.json`；`index.html`、`data.js`、`build.py`、`tools/` 一律不碰 | 幾何與程式被改壞，地圖打不開或國界錯亂，且無法用 JSON 復原 |
| **② 完整且合法** | 輸出必須是**完整**的一份 `content.json`：從 `{` 到 `}`、13 個欄位都在、不省略、不加解說 | 老師貼回檔案時缺段，內容遺失；JSON 壞掉時網頁會悄悄退回舊的內建內容 |
| **③ id 不發明** | entity／region／frame id 只能用第 7 節清單；既有 event id 不改 | 打錯 entity id 會讓該地變成別國顏色；打錯 region id 會被忽略 |

## 9. 五個常見任務的正確做法

> 對應的現成指令在 `docs-ai/prompts/`，複製貼上即可。

### 任務 1：改事件文字（最常用）

1. 在 `events` 陣列找到 `id` 相符的事件。
2. 只改要改的欄位：`summaryZh`（右欄摘要，建議 80–120 字）、`detailZh`（詳細說明，建議 200–400 字）、`titleZh`（中文標題）。
3. `id`、`year`、`eraLabel`、`terms` 除非明確要求，否則原樣保留。
4. 同一個事件的中英文要對得上（`titleEn` 與 `titleZh` 不可一個寫戰爭、一個寫條約）。

### 任務 2：新增事件

1. 先在 `events` 陣列依 `year` 插入新物件，**不是**加在最後（程式雖會顯示，但清單順序會亂）。
2. 完整欄位缺一不可：`id`、`year`、`dateLabel`、`titleEn`、`titleZh`、`type`、`summaryZh`、`detailZh`、`terms`，以及附帶的 `eraLabel`（對應 `eraLabels` 的字串）。
3. `type` 只能是：`war`（戰爭）、`treaty`（條約）、`revolt`（起義）、`unification`（統一）、`crisis`（危機）、`dynastic`（王朝）。
4. `year` 必須落在 `eras` 範圍內；`terms` 放英文專有名詞（例：`["Treaty of Paris"]`），不要放整句中文。

### 任務 3：改某地塊在某年之後的歸屬

1. 先確認**地塊 id**（region id，第 7-2 節）與**國家 id**（entity id，第 7-1 節），不要憑記憶猜。
2. 在 `owners` 找到該地塊的陣列，加入或修改一筆 `[年份, "entity id"]`，並依年份由小到大排序。
3. 第一筆保持 `1853`（或 `eras` 最開頭那年）；年份要與條約生效年一致（例：威尼托 1866 年歸義大利）。
4. 若該地塊屬於德意志邦聯／帝國體系，檢查 `groups` 是否也要同步加一筆（例：`"holstein": [[1867,"ngc"],[1871,"germany"]]`）。
5. **不可以**改國界形狀、顏色或新增地塊；那要動幾何資料。

威尼托（`venetia`）由奧地利歸義大利，就是這樣寫：

```json
"venetia": [[1853, "austria"], [1866, "italy"]]
```

### 任務 4：新增一個年代斷點

1. 在 `eras` 依大小插入新年份（例：在 `1861` 與 `1864` 之間插入 `1862`）。
2. 在 `eraLabels` 的**同一個位置**插入對應的英文短標（長度必須等於 `eras`）。
3. 時間軸刻度、滑桿範圍會自動依新的 `eras` 重畫；**但地圖形狀不會變**——若新年代有領土變化，還要另外改 `owners`（任務 3）。
4. 檢查所有 `events` 的 `year`、`owners`／`groups` 的年份是否還在範圍內。

### 任務 5：改地圖上的英文國名

1. 地圖標籤來自 `labels`：key 是 entity id，值是**全大寫英文**（例：`"prussia": "PRUSSIA"`）。
2. 右欄的正式國名是 `entities.<id>.formalEn`，中文名是 `entities.<id>.zh`，三者要一致。
3. 標籤越短越好（建議 3–22 個字元）；太長會與其他標籤重疊，程式會自動隱藏優先順序較低的標籤。
4. 北德意志邦聯／德意志帝國的外框名稱在 `frames`（`en`／`zh`），不在 `labels`。

## 10. 自我檢查清單

### 10-1 AI 交付前（每一項都要通過）

- [ ] 輸出是**完整**檔案：從第一個 `{` 到最後一個 `}`，13 個頂層欄位都在。
- [ ] 沒有 JSON 語法錯：逗號正確、最後一筆沒有多餘逗號、引號都是半形雙引號。
- [ ] 只改了被要求的內容；其他欄位與資料長度（事件數、實體數、owners 筆數）沒有變。
- [ ] 所有用到的 id 都在第 7 節清單內；`eras` 內沒有重複、且由小到大。
- [ ] `eraLabels.length` **等於** `eras.length`。
- [ ] `owners`／`groups` 每年份都在 `eras` 範圍內、且各陣列依年份遞增。
- [ ] 每個地塊的第一筆 `owners` 是 `1853`（或 `eras` 第一年）。
- [ ] 文字欄位沒有 HTML 標籤、沒有 Markdown 粗體、沒有未跳脫的雙引號。

### 10-2 老師收到後（不用寫程式，3 分鐘）

1. 把 AI 回覆的 JSON 貼到 <https://jsonlint.com>（線上 JSON 驗證器）按 Validate：**綠色**才算過關。
2. 用瀏覽器雙擊打開地圖 → 按左欄下方「✎ 編輯課程內容」→「匯入／匯出」→ 貼上 JSON → 按「匯入」，確認地圖標籤、事件清單與各年代畫面正確（此步只存在你的瀏覽器）。
3. 按「下載 content.json」取得正式檔 → 與舊檔比對無誤 → 上傳 GitHub 覆蓋。

### 10-3 給懂一點電腦的人／AI 的快速驗證（選用）

```python
import json
d = json.load(open('content.json', encoding='utf-8'))          # 1. 能不能解析
assert d['schema'] == 1 and len(d['eras']) == len(d['eraLabels'])  # 2. 欄位與長度
assert all(a < b for a, b in zip(d['eras'], d['eras'][1:]))     # 3. 年代遞增
ids = set(d['entities'])
assert len(ids) == 45
for r, tr in d['owners'].items():                               # 4. 國家 id 存在、年份遞增
    assert all(e in ids for _, e in tr), (r, tr)
    assert all(a[0] < b[0] for a, b in zip(tr, tr[1:])), r
assert len({e['id'] for e in d['events']}) == len(d['events'])  # 5. 事件 id 不重複
print('OK', len(d['events']), 'events,', len(d['owners']), 'regions')
```

> 若只是新增／修改文字，`python -c "import json;json.load(open('content.json',encoding='utf-8'));print('OK')"` 一行就能確認格式。

## 11. 不要做什麼

| 不要做 | 為什麼 | 正確做法 |
|---|---|---|
| 改 `index.html`、`src/`、`tools/`、`build.py` | 程式與幾何在其中，改壞無法用 JSON 復原 | 只改 `content.json` |
| 改 `schema` 的值、改欄位名稱、新增自創欄位 | 程式不認得，可能整份內容被忽略 | 照第 6 節的欄位用 |
| 新增 entity／region id、新增國家 | 幾何資料裡沒有對應形狀，畫不出來 | 請維護者處理；文字上可補充說明 |
| 改國界座標、顏色、地塊形狀 | 幾何固定在 `index.html` 內 | 只能改文字與歸屬 |
| 把 HTML（`<b>`、`<br>`）或 Markdown（一種用符號排版的純文字格式，例如 `**粗體**`）放進文字欄位 | 會被當成純文字原樣顯示 | 用純文字，必要時用中文標點 |
| 用 Word 打字後直接改副檔名 | 會變成格式錯誤、含隱藏字元 | 用記事本／VS Code，存 UTF-8 |
| 只回覆「改了這一段」的片段 | 老師無法貼回完整檔案 | 要求完整檔案，或明確說「請給我完整檔」 |
| 一次同時改十個主題 | 出錯時難以定位、難以驗證 | 一次一個主題，改完先驗證 |
| 改既有事件的 `id` | 外部連結與清單可能對不上 | 只改內容，不改 id |
| 在 `note` 塞設定或程式碼 | `note` 只是給人看的備註 | 一行版本說明即可 |

## 12. 附錄：座標小抄與症狀對照

### 12-1 經緯度怎麼填

- `lon`（經度）：英國以西是負值，往東遞增；本圖大約 `-25`（大西洋）到 `45`（黑海東側）。
- `lat`（緯度）：赤道以北都是正值；本圖大約 `34`（北非）到 `72`（北歐）。
- 參考點：倫敦 `-0.13, 51.51`、巴黎 `2.35, 48.86`、柏林 `13.40, 52.52`、維也納 `16.37, 48.21`、羅馬 `12.50, 41.90`、伊斯坦堡 `28.98, 41.01`。
- 城市與戰役標記要落在**陸地**上；海域標籤（`seas`）要放在海面空白處，不要壓到國名。
- `battle`／`siege` 標記只在該 `year` 前後 2 年顯示；`city` 要縮放 1.25 倍以上才出現圓點。

### 12-2 常見症狀對照

| 症狀 | 可能原因 | 解法 |
|---|---|---|
| 地圖打開但內容像舊的 | `content.json` 不是合法 JSON，網頁讀不到就退回內建內容 | 用 jsonlint 或第 10-3 節驗證 |
| 某一年某塊地顏色不對 | `owners` 年份或 entity id 打錯；第一筆不是 1853 | 對照第 7 節與任務 3 |
| 地圖英文國名沒變 | 改到 `entities` 而非 `labels`；或該 entity 的 key 打錯 | 地圖標籤只認 `labels` |
| 時間軸沒有新斷點 | `eras` 未遞增、有重複，或 `eraLabels` 數量沒同步 | 兩者一起改、長度相同 |
| 上傳 GitHub 後沒變化 | 還在部署（1–2 分鐘）、瀏覽器快取、或改到別的檔案 | 等 1–2 分鐘並按 `Ctrl + F5` |
| 本機雙擊開檔看不到新 content.json | 以 `file://` 開檔時瀏覽器不會讀取 `content.json` | 用網頁編輯器「匯入」，或起本機伺服器、或上傳後看線上版 |

## English Summary (for non-Chinese AI)

This project is a single-page interactive historical atlas of Europe, 1853–1871, used in university courses on modern diplomatic history. It has exactly two editable layers:

- `index.html` — the application, styles, and **all border geometry**. Never edit it.
- `content.json` — UTF-8 JSON holding every teacher-editable string plus territorial timelines. This is the only file to edit.

`content.json` has 13 top-level keys and **all must be preserved**: `schema` (always 1), `note`, `eras` (ascending integer years), `eraLabels` (English short labels; length must equal `eras`), `labels` (entity id → uppercase map label), `entities` (45 political entities: `zh`, `formalEn`, `formalZh`, `capitalEn`, `capitalZh`, `ruler`, `notes`), `frames` (2 frame names: `ngc`, `germany`), `owners` (region id → `[[startYear, entityId], …]`, ascending), `groups` (region id → `[[startYear, frameId], …]`, may be empty), `seas`, `cities` (`kind`: `city` | `battle` | `siege`), `events` (id, year, dateLabel, titleEn, titleZh, type, summaryZh, detailZh, terms), and `regionNotes` (region id → note).

Valid entity ids (45) and region ids (49) are listed in section 7; never invent them. `owners`/`groups` are keyed by **region**, not country. The first `owners` entry should normally be `[1853, …]`, because ownership before the first transition falls back to that entry.

Three hard rules: (1) edit only `content.json`, never `index.html`;
(2) the reply must be the **complete** valid JSON file — no fragments, no omissions, no extra commentary, no HTML inside text fields;
(3) never invent entity, region, frame, or existing event ids.

Do not change border geometry, colours, or add countries/regions — those require rebuilding the geometry pipeline. If asked to do so, refuse and explain. Before answering, verify: JSON parses, all 13 keys present, `eras` ascending without duplicates, `eraLabels.length === eras.length`, all ids exist, `owners` years sorted and within the `eras` range, and no other content changed. Five common tasks are documented in section 9, and ready-to-copy prompts live in `docs-ai/prompts/`.
