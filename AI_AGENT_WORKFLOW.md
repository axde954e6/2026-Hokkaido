# 北海道行程網站 AI 分工文件

本專案建議切成 3 個 agent 並行，但最後只由「網頁整合 agent」負責改 HTML、commit、push。這樣可以避免多個 AI 同時改同一份 `index.html` / `food.html` 造成衝突。

## 專案基本資料

- 旅遊期間：2026/7/18-2026/7/26。
- 住宿：
  - 7/18-7/20：OMO7 旭川 by 星野リゾート。
  - 7/21 起：札幌京王廣場飯店。
- 移動：
  - 前段自駕。
  - 7/22：札幌往返小樽。
  - 7/23：20:00 前還車，還車點是 `トヨタレンタカー札幌駅南口店`。
  - 7/24 起：以步行、地鐵、JR 為主。
- GitHub Pages repo：`https://github.com/axde954e6/2026-Hokkaido`
- Pages URL：
  - 行程頁：`https://axde954e6.github.io/2026-Hokkaido/`
  - 美食頁：`https://axde954e6.github.io/2026-Hokkaido/food.html`
- 主要檔案：
  - `index.html`：每日行程、住宿、景點備案。
  - `food.html`：美食清單。
  - `hokkaido-hero.png`：行程頁主視覺。
  - `.github/workflows/deploy-pages.yml`：GitHub Pages 部署。

## 共通規則

- 手機閱讀優先：短段落、清楚分區、大按鈕、避免寬表格。
- 每個景點與店家都要有 Google Maps 連結。
- 自駕日若有 MapCode，要保留 MapCode，且 MapCode 本身要可點擊到 Google Maps。
- 新增內容如果不是原始 Excel 已有，先標記 `codex`，方便使用者辨識。
- 不要放訂位代號、英文 reservation code、個資或付款資訊到網頁上。
- 日期要標出星期幾，並標明當天主城市：旭川、小樽、札幌。
- 營業時間不要只寫「請確認」。查不到精準時間時，也要給可判斷的範圍，例如 `約 11:00-15:00`、`夜中心，日祝休`。
- 所有外部資訊要附來源連結。正式頁面可用按鈕或底部來源清單呈現。
- 若資訊會變動，例如營業時間、Tabelog 分數、閉店狀態、休館日，更新前要重新查證。

## Agent 1：查美食

### 責任

- 找旭川、小樽、札幌的餐廳。
- 依日式美食種類分類，例如壽司、拉麵、燒肉、涮涮鍋、壽喜燒、湯咖哩、鰻魚、甜點、居酒屋。
- 優先找 Tabelog 評價高、百名店、Award、在地名店，且確認仍營業中的店。
- 札幌需要比旭川、小樽更多備案，尤其是薄野、札幌站、大通、狸小路周邊。

### 查詢規則

- 優先來源：
  - Tabelog 店家頁。
  - 店家官方網站。
  - Google Maps。
  - Gurunavi、Hot Pepper 等可補營業時間，但不要取代 Tabelog 分數。
- 必填欄位：
  - 城市：旭川 / 小樽 / 札幌。
  - 食物分類。
  - 店名。
  - Tabelog 分數。若只有大致可信範圍，寫 `3.6台`；若有精準值，寫 `3.76`。
  - 是否百名店或 Award。
  - 大概營業時間與公休日。
  - 推薦特色。
  - Tabelog 連結。
  - Google Maps 連結。
- 檢查閉店：
  - Tabelog 標題出現 `【閉店】` 或正文明確寫已閉店，不要放入推薦清單。
  - Tabelog URL 404 或店名對不上，標記為需要人工複查，不要直接加入。
- 輸出給整合 agent 時，不要直接改 `food.html`，先用 Markdown 或 JSON 清單交付。

### 建議交付格式

```markdown
## 札幌｜拉麵

- 店名：麺屋 彩未
- Tabelog：3.78
- 標記：ラーメン HOKKAIDO 百名店
- 營業：約 11:00-15:15，部分日晚間營業
- 區域：美園
- 推薦特色：札幌味噌拉麵代表名店，排隊名店，適合認真吃一餐拉麵。
- Tabelog：https://...
- Google Maps：https://...
- 是否新增內容：codex
```

## Agent 2：查景點與安排景點

### 責任

- 查旭川、小樽、札幌景點。
- 補行程空白處，並依住宿、移動方向、還車時間安排順序。
- 提供景點備案，分成旭川、小樽、札幌。
- 產出每日行程建議給整合 agent。

### 行程約束

- 7/18：抵達新千歲，租車後前往旭川，住 OMO7 旭川。
- 7/19-7/20：以旭川、美瑛、富良野周邊自駕為主。
- 7/21：旭川到札幌，住札幌京王廣場飯店。
- 7/22：札幌往返小樽。
- 7/23：札幌西側景點，20:00 前還車。
- 7/24-7/26：不再自駕，以札幌市區、步行、地鐵、JR 為主。

### 查詢規則

- 優先來源：
  - 官方觀光網站。
  - 景點官方網站。
  - Google Maps。
  - MapCode 來源網站或可靠租車 MapCode PDF。
- 必填欄位：
  - 日期。
  - 星期幾。
  - 主城市。
  - 時段：早上 / 午餐 / 下午 / 晚餐 / 晚上 / 20:00 前等。
  - 景點名稱。
  - 安排理由。
  - Google Maps 連結。
  - 自駕日前段需要 MapCode。
  - 是否新增內容：codex。
- 備案景點不要塞進主行程，放在「景點備案」區，依城市分類。

### 建議交付格式

```markdown
## 2026/7/23（四）｜札幌

- 時段：早上
- 點位：白色戀人公園
- 安排理由：札幌西側動線，適合搭配圓山區。
- MapCode：9 603 301*88
- Google Maps：https://...
- 來源：https://...
- 是否新增內容：原行程 / codex
```

## Agent 3：整合網頁並推上去

### 責任

- 接收美食 agent 與景點 agent 的輸出。
- 統一內容、語氣、格式、連結。
- 修改 `index.html` 與 `food.html`。
- 檢查手機閱讀體驗。
- commit 並 push 到 `main`，讓 GitHub Pages 部署。

### HTML 整合規則

- 保持既有版型，不要重做整站。
- `index.html`：
  - 頁首摘要要符合最新移動資訊。
  - 最上方保留住宿地點區塊。
  - 每天標題格式：`2026/7/23（四）｜札幌｜...`
  - 每天 meta tag 要有主城市與交通狀態。
  - 新增行程點要加 `codex` 標記。
  - 7/23 是還車日，7/24 起不能再標自駕。
- `food.html`：
  - 分城市：旭川、小樽、札幌。
  - 城市下再分食物種類。
  - 每張店家卡要有顯眼 Tabelog 分數 badge。
  - 每家都要有 Google Maps 連結。
  - Tabelog 或官方頁能附就附。
- 不要把訂位代號放入頁面。
- 不要移除使用者原始資料，除非使用者明確要求。

### 推送流程

```powershell
git status --short
git diff -- index.html food.html
git add index.html food.html
git commit -m "Update itinerary and food guide"
git push origin main
```

只 stage 實際修改的網站檔案。不要把原始 Excel、舊版 HTML、備忘草稿誤加進 commit。

目前常見未追蹤檔案如下，除非使用者要求，不要 stage：

- `日本北海道2026_7_18-7_26.xlsx`
- `北海道2026行程_手機版.html`
- `專案備忘.md`

## 三個 agent 怎麼協作

1. 使用者提出需求，例如「札幌多找壽司和拉麵」。
2. 美食 agent 查資料，輸出結構化清單，不改 HTML。
3. 景點 agent 若需求涉及行程動線，補充日期、交通、景點調整，不改 HTML。
4. 整合 agent 合併兩邊結果，檢查是否和住宿、還車日、城市順序衝突。
5. 整合 agent 修改 HTML，檢查連結與數量。
6. 整合 agent commit/push。
7. 使用者在 GitHub Pages 手機查看。

## 可以直接給各 agent 的提示詞

### 給美食 agent

```text
你是北海道行程網站的美食資料 agent。請只負責查餐廳資料，不要改 HTML。
目的地是 2026/7/18-7/26 北海道，城市包含旭川、小樽、札幌。
請依城市與食物分類整理，優先找 Tabelog 高分、百名店、Award、仍營業中的店。
每家必須提供：店名、城市、分類、Tabelog 分數、是否百名店、營業時間範圍、公休日、推薦特色、Tabelog 連結、Google Maps 連結。
札幌請多找薄野、札幌站、大通、狸小路附近店家。
不要加入 Tabelog 顯示閉店、URL 404、店名對不上的店。
輸出 Markdown 清單，新增建議請標記 codex。
```

### 給景點 agent

```text
你是北海道行程網站的景點與動線 agent。請只負責查景點和安排每日動線，不要改 HTML。
旅期是 2026/7/18-7/26。7/18-7/20 住 OMO7 旭川，7/21 起住札幌京王廣場飯店。
7/21 是旭川到札幌，7/22 是札幌往返小樽，7/23 20:00 前還車，7/24 起不自駕。
每個點都要提供 Google Maps 連結；自駕日有 MapCode 的點請提供 MapCode。
每日請標日期、星期、主城市、時段、安排理由、來源連結。
新增內容請標記 codex。不要把備案塞進主行程，備案請依旭川、小樽、札幌分區。
```

### 給整合網頁 agent

```text
你是北海道行程網站的整合與發布 agent。請負責修改 index.html / food.html，並推到 GitHub Pages。
先讀 AI_AGENT_WORKFLOW.md，再讀目前的 index.html / food.html。
接收美食 agent 與景點 agent 的清單後，合併到既有版型，不要重做整站。
手機閱讀優先。每個景點/店家都要有 Google Maps 連結；自駕日前段 MapCode 要可點擊。
新增內容標記 codex。不要放訂位代號。7/23 是還車日，7/24 起不標自駕。
修改後執行 git status、git diff，只 stage 實際修改的網站檔案，commit，push origin main。
不要 stage 原始 Excel、舊版 HTML、或不相關草稿。
```

## 使用建議

- 如果三個 AI 是同時工作，請讓美食 agent 和景點 agent 只產出資料，不要碰 repo。
- repo 寫入權限只給整合 agent。
- 每次更新前，整合 agent 應先看最新 `main`，避免覆蓋前一次已發布內容。
- 大改版前先確認是否要維持單頁 HTML；目前 GitHub Pages 靜態 HTML 已足夠，不需要引入框架。
