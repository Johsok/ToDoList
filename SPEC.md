# 週間時段 To Do List — 產品規格書（SPEC）

> 版本：v0.14（已確認）
> 建立日期：2026-06-26  
> 部署目標：單一 `index.html` → **GitHub Pages**  
> 資料後端：**新建 Supabase 專案**（無登入、自動同步）

---

## 1. 專案概述

### 1.1 產品定位

以**週曆 × 時段格狀表**為核心的待辦事項網頁。使用者將事項安排到「某一天 × 某個時間段」，所有資料透過 **Supabase 雲端**自動讀寫，體驗類似 **Google 試算表自動存檔**。

- **無需登入畫面**：開啟 `index.html` 即自動以 `instance_id` 連線 Supabase 並載入資料
- **自動存檔**：每次修改自動同步至 Supabase（見 §3.8）
- **不使用本機暫存**：待辦、設定、Theme 與常用項目皆以 Supabase 為唯一資料來源；`localStorage` 僅保留無登入所需的 `todo_instance_id`
- **主檔名**：`index.html`
- **主題**：深色模式、精美排版、手機 RWD

### 1.2 已確認選項（A～G + 新增）

| # | 項目 | 決定 |
|---|------|------|
| A | 登入 | **不需登入畫面**；`instance_id` 背景自動處理 |
| B | 週起始與切換 | **週一**起算；桌機與手機用上週／今天／下週符號按鈕切換 |
| C | 同格多筆 | **可以** |
| D | 事項操作 | **編輯 / 新增 / 刪除 / 勾選** |
| E | 拖曳 | 同格排序；跨日期／時段移動 |
| F | Supabase | **新建專案**；步驟見 §13 |
| G | 捲動 | 橫向捲週欄；縱向捲時段與下週／下個月 |
| **H** | **當日一鍵操作** | 點日期欄頭 → **清除**／**複製**當日行程 |
| **I** | **現在時段高亮** | 當前時間所在格：特殊背景＋文字色；其餘格恢復預設色 |
| **J** | **空白格新增** | 點空白格顯示含「新增」的浮動功能列，再新增該格事項 |
| **K** | **自動存檔** | 開頁即同步讀取；改動即寫入 Supabase（類 Google Sheets） |
| **L** | **年月日選擇** | 可選年／月／日跳轉並編輯該日時段事項 |
| **M** | **顏色標籤** | 重要事項可標示不同顏色 |
| **N** | **常用項目** | 可在設定中維護常用待辦，並於每筆項目的下拉選單套用 |
| **O** | **文字大小設定** | 設定中可選待辦文字大小，並同步記憶至 Supabase |
| **P** | **單日／整週顯示** | 日期區間右側可切換「今」與「週」，並同步記憶至 Supabase |
| **Q** | **桌機頂部控制精簡** | 桌機將上週／今天／下週改為「設定」左側符號按鈕；設定面板改為浮動顯示 |
| **R** | **單日／週切換** | 「今／週」右側提供左右符號按鈕；今模式切換顯示日，桌機週模式切換上一週／下一週 |
| **S** | **項目拖曳把手隱藏** | 待辦項目不顯示左側 `⋮⋮` / `::` 文字，但整張項目仍可拖曳 |

### 1.3 技術棧

| 項目 | 選擇 |
|------|------|
| 前端 | 單一 `index.html`（HTML + CSS + Vanilla JS） |
| 資料庫 | Supabase（PostgreSQL） |
| SDK | CDN：`@supabase/supabase-js@2`（含備援 CDN） |
| 同步 | 開頁 `select` 全量載入；變更依情境即時／防抖 `insert`、`update`、`delete` |
| 字體 | `Microsoft JhengHei`、`Noto Sans TC` |
| 部署 | GitHub Pages |

---

## 2. 功能需求對照

| # | 需求 | 規格說明 | 狀態 |
|---|------|----------|------|
| 1 | 新增寫入 Supabase | 新增後自動 `insert` 並標記已同步 | ✅ |
| 2 | 時間間隔可調且記憶 | 10 / 15 / 30 / 60 分；區段 06:00–22:00 | ✅ |
| 3 | 週一～週日週視圖 | 週一首欄；可切換上／下週 | ✅ |
| 4 | 手機 RWD | 橫向捲週欄不換週、縱向捲動、觸控友善 | ✅ |
| 5 | 每格增刪改 | 新增／編輯／刪除／勾選皆自動存檔 | ✅ |
| 6 | 深色精美排版 | 漸層、毛玻璃、藍色強調 | ✅ |
| 7 | 拖曳 | 同格排序；跨格移動 | ✅ |
| 8 | 已完成紀錄 | 勾選保留、刪除線樣式 | ✅ |
| 9 | **當日清除／複製** | 點日期欄頭選單操作（手機可用） | ✅ 新增 |
| 10 | **現在時段高亮** | 當前時間格特殊配色；離開後恢復 | ✅ 新增 |
| 11 | **空白格點擊新增** | 點空格直接進入新增輸入 | ✅ 新增 |
| 12 | **自動同步存檔** | 開頁讀取；改動自動寫入 Supabase | ✅ 新增 |
| 13 | **年月日跳轉** | 日期選擇器指定某日編輯 | ✅ 新增 |
| 14 | **顏色標籤** | 事項可標重要色 | ✅ 新增 |
| 15 | **常用項目設定** | 設定中新增／刪除常用項目，寫入 Supabase，任務卡可下拉套用 | ✅ 新增 |
| 16 | **文字大小設定** | 可選小／中／大／特大，設定同步至 Supabase | ✅ 新增 |
| 17 | **每日欄寬自動貼齊** | 每日欄依該欄最長待辦文字自動收斂，避免 7 欄平均撐寬 | ✅ 新增 |
| 18 | **單日／整週顯示模式** | 「今」顯示目前單日事項且同時段橫向排列；「週」顯示整週且同時段縱向排列 | ✅ 新增 |
| 19 | **桌機設定浮動面板** | 設定點擊後以浮動面板顯示，不佔用右側版面；頂部控制盡量置中 | ✅ 新增 |
| 20 | **今／週旁左右切換** | 新增左右按鈕；今模式切換單日顯示，桌機週模式切換整週 | ✅ 新增 |
| 21 | **隱藏項目拖曳符號** | 待辦卡片移除左側 `::` / `⋮⋮` 視覺文字，但拖曳排序與跨格移動保留 | ✅ 新增 |

---

## 3. 核心概念

### 3.1 時間段（Time Slot）

- **每日區段**：`day_start` ~ `day_end`（預設 `06:00` ~ `22:00`）
- **間隔**：`10` / `15` / `30` / `60` 分鐘（預設 `30`）
- 變更設定後不刪除既有事項；時間對齊至最接近時段

### 3.2 週視圖

- 一週 = 週一～週日
- 導覽：◀ 上週｜本週｜下週 ▶；桌機與手機以「設定」左側符號按鈕切換，不用左右滑動換週或換月
- 格子座標 = `todo_date` + `slot_start`
- 日期欄頭格式為 `MM/DD(週)`，例如 `06/12(一)`；不顯示額外副文字。

### 3.3 瀏覽與邊界滑動

- 主內容區可縱向連續捲動多週
- 懶加載：目前週 ± 2 週；接近底部載入更多
- 「本月」「下月」快速跳轉按鈕（v1 納入）
- 手機橫向捲到週表最左或最右時，不自動切換上週、下週、上月或下月。

### 3.4 年月日跳轉（L）

頂部工具列提供**日期選擇器**：

```
[ 2026 ▼ ] [ 6月 ▼ ] [ 26日 ▼ ]  [ 前往 ]
```

| 行為 | 說明 |
|------|------|
| 選年／月／日 | 下拉或原生 `<input type="date">`（手機友善） |
| 前往 | 捲動至該日所在週，並將該日欄置於可見區域 |
| 編輯 | 可在該週任意時段格新增／修改事項 |
| 預設 | 開啟時聚焦**今日**所在週 |

### 3.5 當日一鍵操作（H）

點擊欄頭**日期**（如 `06/26(四)`）開啟操作選單（桌機：下拉；手機：底部 Sheet）：

| 選項 | 行為 | 確認 |
|------|------|------|
| **清除當日行程** | 刪除該日所有 `todos`（含已完成） | 需 `confirm`：「確定清除 6/26 全部行程？」 |
| **複製該日項目** | 將該日所有事項複製到目標日 | 彈出日期選擇器選目標日 → `insert` 複本（`is_done` 重置為 `false`） |

- 手機：日期欄頭觸控區 ≥ 44px；Sheet 大按鈕
- 複製時保留：`content`、`slot_start`、`color_tag`、`sort_order`
- 複製時不保留：`is_done`（新複本為未完成）

### 3.6 現在時段高亮（I）

依 **Asia/Taipei（UTC+8）** 計算當前時間，對齊至設定之 `interval_minutes` 格線。

| 狀態 | 樣式 |
|------|------|
| **當前時段格**（僅今日欄） | 背景：琥珀色半透明 `rgba(255, 183, 77, 0.25)`；左側時間標籤：橘色 `#ffb74d`；文字：`#fff8e1` |
| **非當前時段格** | 恢復預設深色格背景 `rgba(255,255,255,0.03)`、預設文字 `#e0e0e0` |
| **今日欄（非當前格）** | 維持淡藍欄高亮（與現在格可疊加，現在格優先） |

- 每 **60 秒**重新計算並更新高亮列（或跨格時 `setTimeout` 精準切換）
- 僅在**今日**欄顯示現在時段高亮；其他日期不顯示

### 3.7 空白格點擊新增（J）

| 觸發 | 行為 |
|------|------|
| 點擊格子背景（含空格或有事項格的空白處） | 該格最上方出現浮動功能列，至少提供「新增」 |
| 點擊「新增」 | 開啟「輸入其他文字」輸入框並自動 `focus` |
| 預填 | 日期 = 該欄日期；時段 = 該列時間 |
| 輸入方式 | 可從「選常用」下拉快速帶入，也可直接輸入其他文字 |
| Enter | 新增事項並自動存檔 |
| Esc | 取消輸入、收起輸入框 |

### 3.8 自動同步存檔（K）

設計目標：**像 Google 試算表**，使用者無需按儲存。

#### 3.8.1 `instance_id`（背景自動，不顯示於 UI）

1. 首次開啟：若 `localStorage.todo_instance_id` 不存在 → 自動產生 UUID v4 並存入
2. **不顯示** instance_id；使用者無需手動備份
3. 所有資料查詢／寫入皆帶此 `instance_id`
4. 同一瀏覽器再次開啟 → 自動讀取同一批資料

> 換裝置或清除瀏覽器資料會產生新 `instance_id`（新資料空間）。此為無登入架構之取捨，不在 UI 暴露。

#### 3.8.2 開頁同步流程

```
開啟 index.html
  → 初始化 Supabase Client
  → 讀取 / 建立 instance_id
  → SELECT app_settings
  → SELECT todos（instance_id + 目前可視日期範圍，含預載週）
  → 渲染週表
  → 狀態列以綠燈顯示「同步雲端」
```

#### 3.8.3 自動存檔策略

| 操作類型 | 存檔時機 | 方式 |
|----------|----------|------|
| 新增事項 | 立即 | `insert` |
| 刪除事項 | 立即 | `delete` |
| 勾選／取消 | 立即 | `update` |
| 拖曳換格／排序 | 放手後立即 | `update`（多筆） |
| 編輯文字（inline） | **防抖 800ms** 停止輸入後 | `update` |
| 顏色標籤變更 | 立即 | `update` |
| 清除當日／複製 | 操作完成後 | `delete` / 批次 `insert` |
| 設定變更 | 按儲存後 | `update` app_settings `id=1` |
| 常用項目變更 | 新增／刪除後立即 | `update` app_settings.common_items `id=1` |
| 文字大小變更 | 按儲存後 | `update` app_settings.font_size `id=1` |

**備援機制**：若 800ms 防抖期間持續輸入，最遲每 **5 秒**強制將未同步的 dirty 狀態寫入一次。

#### 3.8.4 同步狀態指示（頂部或底部）

| 狀態 | 顯示 |
|------|------|
| 同步中 | `雲端儲存中…`（淡色動畫點） |
| 已同步 | 綠燈 + `同步雲端` |
| 未連線 | 紅燈 + `雲端未連線`；不寫入本機暫存 |
| 失敗 | `無法同步，將自動重試`；不使用 `localStorage` 暫存待辦或設定 |

### 3.9 顏色標籤（M）

每筆事項可標記為**一般**或**重要**（可擴充多色）。

| `color_tag` 值 | 顯示 | 用途 |
|----------------|------|------|
| `default` | 預設藍白文字 | 一般事項 |
| `important` | 左側色條 `#ff6b6b` + 淡紅底 `rgba(255,107,107,0.15)` | 重要事項 |

**操作方式：**

- 長按事項或點擊色標圖示 🏷 → 切換 `default` ↔ `important`
- 變更後立即 `update` 至 Supabase

未來 v2 可擴充：`work`、`personal`、`health` 等更多色票。

### 3.10 待辦事項欄位

| 屬性 | 說明 |
|------|------|
| `content` | 文字（inline 可編輯） |
| `todo_date` | 所屬日期 |
| `slot_start` | 時段開始 |
| `is_done` | 是否完成 |
| `sort_order` | 同格排序 |
| `color_tag` | 顏色標籤（`default` / `important`） |
| `instance_id` | 背景識別（不顯示） |

### 3.11 拖曳行為（E）

| 拖曳類型 | 行為 | 存檔 |
|----------|------|------|
| 同格排序 | 調整 `sort_order` | 放手即 `update` |
| 跨日期／時段 | 變更 `todo_date` / `slot_start` | 放手即 `update` |
| 拖曳區 | 不顯示左側 `⋮⋮` / `::` 文字；整張待辦項目仍可拖曳 | — |

### 3.12 常用項目（N）

- 設定面板預設隱藏，需點「設定」才展開。
- 設定面板提供「常用設定」，可新增／刪除常用項目，且每個項目可上移／下移調整排序。
- 常用項目儲存在 `app_settings.common_items`，型別為 `jsonb` 陣列；陣列順序即為「常用」下拉與設定清單的顯示順序。
- 常用項目新增、刪除或排序後，需立即同步更新 `app_settings.common_items` 到 Supabase。
- 每格待辦功能列預設隱藏；點擊格子背景或格內任一待辦項目後，於表格格子的最上層浮動顯示功能列，不佔用格內排版高度。
- 有待辦項目的格子需顯示「常用」「新增」「刪除」「已完成」；空白格至少需顯示「新增」。
- 「常用」以下拉式選單呈現，選取後會把待辦文字改為該常用項目並自動同步。
- 「新增」會在同一日期與時段開啟「輸入其他文字」手寫輸入框；右側「選常用」下拉可快速帶入文字，同意與取消按鈕浮動在格子右上方，避免撐大該格。
- 格內不顯示「＋新增」文字入口；點擊格子背景時，先顯示浮動功能列，點「新增」後在同一日期與時段開啟新增輸入框。
- 「已完成」會切換刪除線樣式與 `is_done` 狀態。
- 所有下拉式選單需使用深色背景，避免深色主題中白底造成文字不可讀。

### 3.13 文字大小設定（O）

- 設定面板提供「文字大小」選項：`small`、`medium`、`large`、`xlarge`。
- 預設值為 `medium`。
- 設定會同步到 `app_settings.font_size`，下次開啟頁面時套用。
- 文字大小主要影響待辦項目文字，不改變週曆核心邏輯。

### 3.14 單日／整週顯示模式（P）

- 日期區間右側提供兩個小型 UI 按鈕：`今`、`週`。
- `今`：只顯示目前單日事項；同一時段內的多筆事項向右橫向排列，預設日期為今日。
- `週`：顯示週一至週日；同一時段內的多筆事項維持向下縱向排列。
- 「今／週」右側提供左右符號按鈕。
- 在 `今` 模式中，上一日／下一日會切換目前顯示的單一日期；預設顯示今日。
- 在桌機 `週` 模式中，左右按鈕代表上一週／下一週，切換後顯示該整週的週一至週日。
- 在手機 `週` 模式中，左右按鈕可維持選取上一日／下一日；若跨出目前週，切換到該日期所在週。
- 顯示模式儲存在 `app_settings.view_mode`，允許值為 `today` / `week`，預設 `week`。
- 每次切換後立即同步到 Supabase；下次開啟時依 Supabase 記憶值還原。
- 桌機從 `今` 切回 `週` 時，週欄寬不可突然縮到過窄；週視圖日期欄至少需依容器寬度平均分配可視空間，再依內容需要加寬。
- `今` 模式下點擊格子或待辦項目時，新增列與功能列需顯示在最上層、呈現浮動狀態，不能被同時段橫向排列的待辦壓住或擋住。

---

## 4. 資料庫設計（Supabase）

### 4.1 資料表：`app_settings`

| 欄位 | 型別 | 說明 |
|------|------|------|
| `id` | `int` PK | 固定 `1` |
| `day_start` | `time` | 預設 `06:00` |
| `day_end` | `time` | 預設 `22:00` |
| `interval_minutes` | `int` | 預設 `30` |
| `theme_id` | `text` | 預設 `'dark'`；允許 10 種經典配色 theme |
| `common_items` | `jsonb` | 預設 `[]`；常用待辦項目文字陣列 |
| `font_size` | `text` | 預設 `'medium'`；允許 `small`、`medium`、`large`、`xlarge` |
| `view_mode` | `text` | 預設 `'week'`；允許 `today`、`week` |
| `updated_at` | `timestamptz` | 最後更新；由前端每次更新時帶入目前時間 |

> `app_settings` 採全域設定，固定 `id = 1`。本專案以「打開即可用、方便優先」為目標，因此所有公開使用者共用同一組起始時間、結束時間、間隔時間、全域 theme、文字大小、顯示模式與常用項目設定。

### 4.2 資料表：`todos`

| 欄位 | 型別 | 說明 |
|------|------|------|
| `id` | `bigint` PK | 自動遞增 |
| `instance_id` | `text` NOT NULL | 背景識別碼 |
| `todo_date` | `date` NOT NULL | 日期 |
| `slot_start` | `time` NOT NULL | 時段 |
| `content` | `text` NOT NULL | 內容 |
| `is_done` | `boolean` | 預設 `false` |
| `color_tag` | `text` NOT NULL | 預設 `'default'`；`'important'` 等 |
| `sort_order` | `int` | 預設 `0` |
| `created_at` | `timestamptz` | 建立 |
| `updated_at` | `timestamptz` | 更新 |

```sql
CREATE INDEX idx_todos_lookup ON todos (instance_id, todo_date, slot_start);
CREATE INDEX idx_todos_date_range ON todos (todo_date);
```

> 每日每時段的事項以 `todos.todo_date` + `todos.slot_start` 作為格子座標；桌機與手機讀寫同一張 `todos` 表，因此只要使用相同 `instance_id`，同一天同一時段的新增、編輯、拖曳與刪除都會同步到 Supabase 並在兩端一致顯示。本次日切換 UI 不需要新增資料表或欄位。

### 4.3 Row Level Security

無登入 + anon 金鑰；此專案以公開可用與方便操作為優先，GitHub Pages 上線後任何取得網址的人皆可開啟使用。前端會用 `instance_id` 區分待辦資料空間，但不設登入與權限保護，因此不建議存放敏感或私人資料。

| 表 | anon CRUD |
|----|-----------|
| `app_settings` | ✅ 讀寫 |
| `todos` | ✅ 讀寫刪 |

### 4.4 API 操作對照

| 使用者動作 | Supabase 操作 |
|------------|---------------|
| 開頁載入 | `select` settings + todos（by `instance_id` + 日期範圍） |
| 新增 | `insert` |
| 編輯文字 | 防抖後 `update` |
| 勾選／顏色 | 立即 `update` |
| 拖曳 | 立即 `update` |
| 刪除 | 立即 `delete` |
| 清除當日 | `delete().eq('instance_id',id).eq('todo_date', date)` |
| 複製當日 | 讀取來源日 → 批次 `insert` 至目標日 |
| 設定 | `update` app_settings `id=1` |
| 常用項目 | `update` app_settings.common_items `id=1` |
| 文字大小 | `update` app_settings.font_size `id=1` |
| 顯示模式 | `update` app_settings.view_mode `id=1` |

> `updated_at` 不使用資料庫 trigger；所有 `insert` / `update` 由前端帶入目前時間，確保最後更新時間可追蹤。

---

## 5. 介面與互動設計

### 5.1 整體版面

```
┌────────────────────────────────────────────────────────────────┐
│ 週間待辦              [2026▼][6月▼][26日▼][前往]   [‹][今][›] [設定] 已儲存 ✓ │
├────────────────────────────────────────────────────────────────┤
│          2026/06/23（一）~ 06/29（日）      [今] [週] [‹] [›] │
├────────┬─────────┬────┬────┬────┬────┬────┬──────────────────┤
│ 時間 ↕ │06/23(一)│06/24(二)│06/25(三)│06/26(四)│06/27(五)│06/28(六)│06/29(日)│
│        │ [點選]  │        │        │        │        │        │        │
│        │ 清除/複製│        │        │        │        │        │        │
├────────┼─────────┼────┼────┼────┼────┼────┼──────────────────┤
│ 06:00  │         │    │    │    │    │    │                  │
│ 09:00★ │         │    │    │ ■重要│    │    │  ★=現在時段高亮 │
│  ...   │         │    │    │    │    │    │  點空格→新增     │
└────────┴─────────┴────┴────┴────┴────┴────┴──────────────────┘
```

### 5.2 視覺規範

| 元素 | 樣式 |
|------|------|
| 全頁背景 | `linear-gradient(135deg, #0f2027, #203a43, #2c5364)` |
| 一般格 | 背景 `rgba(255,255,255,0.03)`；文字 `#e0e0e0` |
| 今日欄 | 淡藍底 `rgba(79,172,254,0.08)` |
| **現在時段格（今日）** | 背景 `rgba(80,227,194,0.28)`；時間標籤 `#50e3c2`；文字 `#fff8e1`；需使用不同於一般今日欄的提醒色 |
| 重要事項 | 左色條 `#ff6b6b`；底色 `rgba(255,107,107,0.15)` |
| 已完成 | 刪除線 + 透明度 0.55 |
| 同步狀態 | 右上角小字「已儲存」／「儲存中…」 |

- 每日欄頭僅顯示日期與星期，格式為 `MM/DD(週)`，例如 `06/12(一)`；不顯示「今天」或「點選操作」副文字。
- 日期區間文字需比主標題小，右側同列放置「今」「週」顯示模式按鈕與上一日／下一日符號按鈕。
- 左側時間欄文字需水平與垂直置中。
- 每日欄寬需依該欄最長待辦文字自動計算；新增、編輯、刪除、套用常用項目或調整文字大小後都要重新貼齊，避免所有日期欄平均撐得過寬。
- 桌機週視圖欄寬需兼顧內容貼齊與可視寬度；從 `今` 切回 `週` 時，日期欄不得全部縮回過窄的最小欄寬。

### 5.3 單格互動

1. 顯示待辦（依 `sort_order`）
2. 每筆預設只顯示：`⋮⋮`｜置中的單行待辦文字，不換行；過長時省略。
3. 點擊待辦項目後，顯示浮動功能列：`常用` 下拉｜`新增`｜`刪除`｜`已完成`。
4. 點擊格子背景後，也需顯示浮動功能列；有待辦的格子保留 `常用`、`新增`、`刪除`、`已完成`，空白格至少顯示 `新增`。
5. 格內不顯示「＋新增」文字；點擊浮動功能列的 `新增` 時，開啟該日期與時段的新增輸入框。
6. 新增時，同格最上方浮動顯示「輸入其他文字」輸入框；「選常用」下拉只做快速帶入，電腦版與手機版都必須可直接手寫任意文字。
7. 新增相關控制（輸入框、常用下拉、同意、取消）皆不參與格內排版，因此不會改變該格尺寸。
8. `今` 模式因同時段待辦採橫向排列，新增列與功能列需提高層級並解除格內裁切，確保可點擊。

### 5.4 日期欄頭選單（清除／複製）

**桌機**：點日期 → 下拉選單  
**手機**：點日期 → 底部 Action Sheet

```
┌─────────────────────┐
│  2026/06/26（四）    │
├─────────────────────┤
│  🗑 清除當日行程     │
│  📋 複製到其他日期   │
│  取消               │
└─────────────────────┘
```

### 5.5 設定面板

| 設定項 | 預設 |
|--------|------|
| 每日開始 | `06:00` |
| 每日結束 | `22:00` |
| 時間間隔 | `30` 分 |
| 外觀 Theme | 下拉式選單 |
| 文字大小 | `medium` |
| 常用設定 | 可新增／刪除常用項目，並可上移／下移排序 |
| 儲存 | `update` app_settings `id=1` 後重繪 |

桌機版點「設定」時，設定面板需以浮動面板顯示，不佔用週表右側欄位；上週／今天／下週需以符號按鈕放在「設定」左側，頂部日期跳轉區維持置中。

手機版點「設定」時，設定面板需以浮動面板顯示在目前畫面上方，內容包含日期跳轉、同步狀態、Theme、每日時段、文字大小與常用設定；週切換的上週／今天／下週符號按鈕需移到主畫面「設定」左側。手機主畫面不另放「清除本日」與「複製本日」按鈕。

### 5.6 載入與錯誤

- 初次：表格區「載入中…」
- SDK 未載入：重試最多 20 次
- 同步失敗：Toast + 自動重試

---

## 6. 響應式設計（RWD）

| 斷點 | 行為 |
|------|------|
| 桌機 ≥1024px | 7 欄；滑鼠拖曳；日期下拉選單；上週／今天／下週符號按鈕在設定左側；設定面板浮動 |
| 平板 768–1023px | 橫向捲動週欄 |
| 手機 <768px | 主畫面保留標題、上週／今天／下週符號按鈕、設定、日期區間、週表格；週模式垂直瀏覽需可看到整週；日期 Sheet；空白格點擊新增 |

**手勢防衝突**：格子內橫滑只做欄位捲動；滑到最左或最右時不觸發上週、下週、上月或下月切換。

**欄寬策略**：週表不平均分配所有剩餘寬度；每個日期欄依該欄最長文字自動貼齊，必要時由 `.calendar-frame` 提供橫向捲動。

**手機週欄策略**：手機 `週` 模式需優先顯示完整週一～週日，允許縮小日期欄、時間欄與待辦文字；日期欄頭可改為上下兩行（日期／星期）以節省寬度。

**日切換按鈕**：「今／週」右側左右按鈕需在手機與桌機都顯示；今模式切換單日顯示，桌機週模式切換上一週／下一週，手機週模式可切換選取日期。

**手機主畫面**：
1. 第一列顯示「週待辦清單」、上週／今天／下週符號按鈕與「設定」。
2. 第二列需同列顯示日期範圍、「今」「週」切換與上一日／下一日符號按鈕，例如 `2026/06/08 (一) ~ 06/14 (日)`。
3. 第三列開始顯示該週表格，表格需凍結左側時間欄與第一列日期欄。
4. 日期跳轉與同步狀態集中於「設定」面板；「清除本日」與「複製本日」不在手機主畫面或設定面板中顯示，改由點日期欄頭開啟操作。

---

## 7. 非功能需求

| 項目 | 說明 |
|------|------|
| 時區 | Asia/Taipei（UTC+8） |
| 效能 | 懶加載 4 週；批次複製單次 insert ≤100 筆 |
| 自動存檔 | 文字防抖 800ms；最遲 5 秒強制同步 |
| 離線 | v1 不支援；失敗保留本地並重試 |
| 安全性 | 公開無登入；僅 anon key；方便優先，不存放敏感資料 |

---

## 8. 檔案結構

```
ToDoList/
├── index.html
├── SPEC.md
└── todo-ui-preview-theme-settings.png

可選：
└── supabase/
    └── schema.sql（若未來想把雲端 SQL 另存成檔案再建立）
```

---

## 9. 驗收標準

1. 開啟即載入 Supabase 資料，無登入畫面、不顯示 instance_id
2. 新增／編輯／刪除／勾選後，重新整理仍與雲端一致
3. 編輯文字停止輸入約 1 秒內自動存檔；狀態列顯示「已儲存」
4. 點空白格會顯示浮動功能列，點「新增」可新增該日期＋時段事項
5. 今日當前時段格為橘色系高亮；其他格為預設深色
6. 點日期欄頭可「清除當日」與「複製到其他日」（手機可用）
7. 年月日選擇器可跳轉至指定週並編輯
8. 重要事項可標紅色標籤，同步至 Supabase
9. 拖曳、週切換、縱向捲動正常
10. GitHub Pages 部署後完整可用
11. 預設 theme 為深色模式
12. 切換 theme 後重新整理仍保留上次選擇
13. 起始時間、結束時間、間隔時間可修改
14. 修改每日時段設定後，時間軸需即時重繪
15. Supabase 讀取設定失敗時，回到預設值：`06:00`、`22:00`、`30`、`dark`
16. 設定面板預設隱藏，點「設定」才展開
17. 常用項目可新增／刪除並同步到 Supabase，待辦項目可從「常用」下拉套用
18. 文字大小設定可同步到 Supabase，重新整理後仍套用
19. 日期欄頭需顯示為 `06/12(一)` 這類 `MM/DD(週)` 格式
20. 新增事項時可選常用項目，也可直接輸入其他文字
21. 每日欄寬會在新增／編輯項目後依該欄最長文字自動貼齊，表格不因平均分欄而過寬
22. 手機版點「設定」需立即顯示可操作的設定面板
23. 手機主畫面保留標題、上週／今天／下週符號按鈕、設定、日期區間與週表格；時間欄與日期欄需可凍結捲動
24. 「今」「週」顯示模式切換後可同步到 Supabase，重新整理後仍保留
25. 手機橫向滑到表格最左或最右時，不自動切換上週、下週、上月或下月
26. 桌機上週／今天／下週按鈕需在「設定」左側以符號按鈕顯示，設定面板需浮動不佔右側版面
27. 「今／週」右側左右按鈕需在今模式切換顯示日，並在桌機週模式切換上一週／下一週完整週
28. 待辦項目左側不顯示 `::` / `⋮⋮` 文字，但拖曳排序與跨格移動仍可操作
29. 常用設定項目可上移／下移排序，排序後重新整理仍依 Supabase 記憶順序顯示
30. 桌機從 `今` 切回 `週` 時，週格欄寬不會突然縮到過窄
31. 點擊格子或待辦項目時，浮動功能列需提供「新增」；`今` 模式下新增與常用等功能列需在最上層且可點擊
32. 手機日期區間、「今」「週」與上一日／下一日按鈕需在同一列
33. 手機垂直瀏覽 `週` 模式時，可直接看到週一～週日完整 7 欄
34. 桌機 `週` 模式下，「週」右側左右按鈕需切換上一週／下一週，並顯示該週週一至週日

---

## 10. 其他建議（尚未納入 v1）

| 功能 | 說明 | 建議版本 |
|------|------|----------|
| 重複事項 | 每週固定行程 | v2 |
| 提醒通知 | Notification API | v2 |
| Realtime 多分頁 | Supabase Realtime 即時同步 | v2 |
| 匯出 JSON / iCal | 資料備份 | v2 |
| 多色標籤擴充 | 工作／個人／健康 | v2 |
| PWA 離線 | Service Worker | v2 |

---

## 11. 市面上 App 功能參考（v2+）

| 功能 | 說明 |
|------|------|
| 子任務核取清單 | 一事項下多 checkbox |
| 搜尋 | 全文搜尋歷史 |
| 月曆總覽縮圖 | 點日跳週視圖 |
| 統計完成率 | 週報 |
| 協作分享 | 需登入或分享連結 |

---

## 12. Supabase 新建專案 — 詳細設定步驟

### 步驟 1：註冊與建立專案

1. 前往 [https://supabase.com](https://supabase.com) 註冊
2. **New Project** → Name：`todo-weekly`、設定 Database Password、Region 選 Tokyo 或最近區域
3. 等待專案建立完成

### 步驟 2：取得 API 金鑰

1. **Project Settings** → **API**
2. 複製 **Project URL** 與 **anon public** key（僅 anon 可寫入 `index.html`）
3. 切勿公開 `service_role` key

### 步驟 3：建立資料表

**SQL Editor** → 執行以下 SQL。若未來想保留本機備份，可另存為 `supabase/schema.sql`，但 v1 不要求先建立此檔案：

```sql
-- app_settings
CREATE TABLE app_settings (
  id int PRIMARY KEY DEFAULT 1 CHECK (id = 1),
  day_start time NOT NULL DEFAULT '06:00',
  day_end time NOT NULL DEFAULT '22:00',
  interval_minutes int NOT NULL DEFAULT 30 CHECK (interval_minutes IN (10, 15, 30, 60)),
  theme_id text NOT NULL DEFAULT 'dark'
    CHECK (theme_id IN ('dark', 'midnight', 'forest', 'sunrise', 'minimal', 'classic_blue', 'nord', 'dracula', 'solarized', 'high_contrast')),
  common_items jsonb NOT NULL DEFAULT '[]'::jsonb,
  font_size text NOT NULL DEFAULT 'medium'
    CHECK (font_size IN ('small', 'medium', 'large', 'xlarge')),
  view_mode text NOT NULL DEFAULT 'week'
    CHECK (view_mode IN ('today', 'week')),
  updated_at timestamptz NOT NULL DEFAULT now()
);

INSERT INTO app_settings (id) VALUES (1);

-- todos
CREATE TABLE todos (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  instance_id text NOT NULL,
  todo_date date NOT NULL,
  slot_start time NOT NULL,
  content text NOT NULL,
  is_done boolean NOT NULL DEFAULT false,
  color_tag text NOT NULL DEFAULT 'default' CHECK (color_tag IN ('default', 'important')),
  sort_order int NOT NULL DEFAULT 0,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now()
);

CREATE INDEX idx_todos_lookup ON todos (instance_id, todo_date, slot_start);
CREATE INDEX idx_todos_date_range ON todos (todo_date);

-- RLS
ALTER TABLE app_settings ENABLE ROW LEVEL SECURITY;
ALTER TABLE todos ENABLE ROW LEVEL SECURITY;

CREATE POLICY "anon read settings" ON app_settings FOR SELECT TO anon USING (true);
CREATE POLICY "anon insert settings" ON app_settings FOR INSERT TO anon WITH CHECK (true);
CREATE POLICY "anon update settings" ON app_settings FOR UPDATE TO anon USING (true) WITH CHECK (true);

CREATE POLICY "anon read todos" ON todos FOR SELECT TO anon USING (true);
CREATE POLICY "anon insert todos" ON todos FOR INSERT TO anon WITH CHECK (true);
CREATE POLICY "anon update todos" ON todos FOR UPDATE TO anon USING (true) WITH CHECK (true);
CREATE POLICY "anon delete todos" ON todos FOR DELETE TO anon USING (true);
```

> 本專案不使用資料庫 trigger。前端所有 `insert` / `update` 都需一併送出 `updated_at` 欄位，例如 `updated_at: new Date().toISOString()`。

#### 每日每時段同步設定

- 不需為本次上一日／下一日 UI 新增 Supabase 欄位。
- 確認 `todos` 表含有 `instance_id`、`todo_date`、`slot_start`、`content`、`is_done`、`color_tag`、`sort_order`、`created_at`、`updated_at`。
- 確認 `todos` 已啟用 RLS，且 anon role 具備 `SELECT`、`INSERT`、`UPDATE`、`DELETE` policy。
- 桌機與手機同步的關鍵是讀寫同一組 `instance_id`，並以 `todo_date` + `slot_start` 定位每日每時段事項。

#### 既有 Supabase 專案修正

若「常用設定」新增項目或「今／週」切換後顯示「設定同步失敗」，通常是既有 Supabase 專案的 `app_settings` 表尚未補上 `common_items` / `font_size` / `view_mode` 欄位，或缺少 anon 更新權限。請到 **Supabase Dashboard → SQL Editor → New query** 執行：

```sql
ALTER TABLE app_settings
  ADD COLUMN IF NOT EXISTS common_items jsonb NOT NULL DEFAULT '[]'::jsonb,
  ADD COLUMN IF NOT EXISTS font_size text NOT NULL DEFAULT 'medium',
  ADD COLUMN IF NOT EXISTS view_mode text NOT NULL DEFAULT 'week';

DO $$
BEGIN
  IF NOT EXISTS (
    SELECT 1 FROM pg_constraint WHERE conname = 'app_settings_font_size_check'
  ) THEN
    ALTER TABLE app_settings
      ADD CONSTRAINT app_settings_font_size_check
      CHECK (font_size IN ('small', 'medium', 'large', 'xlarge'));
  END IF;

  IF NOT EXISTS (
    SELECT 1 FROM pg_constraint WHERE conname = 'app_settings_view_mode_check'
  ) THEN
    ALTER TABLE app_settings
      ADD CONSTRAINT app_settings_view_mode_check
      CHECK (view_mode IN ('today', 'week'));
  END IF;
END $$;

INSERT INTO app_settings (id) VALUES (1)
ON CONFLICT (id) DO NOTHING;

ALTER TABLE app_settings ENABLE ROW LEVEL SECURITY;

DO $$
BEGIN
  IF NOT EXISTS (
    SELECT 1 FROM pg_policies
    WHERE schemaname = 'public'
      AND tablename = 'app_settings'
      AND policyname = 'anon read settings'
  ) THEN
    CREATE POLICY "anon read settings"
      ON app_settings FOR SELECT TO anon
      USING (true);
  END IF;

  IF NOT EXISTS (
    SELECT 1 FROM pg_policies
    WHERE schemaname = 'public'
      AND tablename = 'app_settings'
      AND policyname = 'anon update settings'
  ) THEN
    CREATE POLICY "anon update settings"
      ON app_settings FOR UPDATE TO anon
      USING (true)
      WITH CHECK (true);
  END IF;
END $$;

NOTIFY pgrst, 'reload schema';
```

### 步驟 4：設定 URL（GitHub Pages 上線後）

**Authentication** → **URL Configuration** → Site URL 填入 Pages 網址。

### 步驟 5：驗證

**Table Editor** 確認兩表存在；可手動 insert 測試列。

### 步驟 6：寫入 index.html

```javascript
const supabaseUrl = 'https://xxxxxxxx.supabase.co';
const supabaseAnonKey = 'eyJhbG...';
let supabaseClient = null;

// instance_id：背景自動，不顯示
function getInstanceId() {
  let id = localStorage.getItem('todo_instance_id');
  if (!id) {
    id = crypto.randomUUID();
    localStorage.setItem('todo_instance_id', id);
  }
  return id;
}
```

---

## 13. GitHub Pages 部署步驟

1. 建立 GitHub repo（可公開），將 `index.html` 放於**根目錄**
2. **Settings** → **Pages** → Branch `main` / root
3. 取得 `https://帳號.github.io/ToDoList/`
4. 回 Supabase 填入 Site URL
5. 測試：自動載入、空白格新增、當日複製／清除、顏色標籤、自動存檔

---

## 14. 修訂紀錄

| 版本 | 日期 | 說明 |
|------|------|------|
| v0.1 | 2026-06-26 | 初版草案 |
| v0.2 | 2026-06-26 | 確認 A～G；無登入；拖曳；Supabase／Pages 步驟 |
| v0.3 | 2026-06-26 | 新增：當日清除／複製、現在時段高亮、空白格新增、Google Sheets 式自動存檔、年月日跳轉、顏色標籤；移除 instance_id UI 備份 |
| v0.4 | 2026-06-26 | 修正：公開無登入使用、`theme_id` schema、10 種經典配色、`updated_at` 前端更新規則、手機與電腦執行確認、iPhone Safari 主畫面注意事項 |
| v0.5 | 2026-06-26 | 調整：待辦項目單行不換行、點擊後才顯示功能列、設定面板預設隱藏、Theme 改下拉選單、新增常用項目設定與 `common_items` 欄位 |
| v0.6 | 2026-06-26 | 調整：有項目格隱藏「＋新增」、新增列支援常用下拉、所有下拉改深色背景、項目置中且欄寬依內容撐開、新增 `font_size` 設定 |
| v0.7 | 2026-06-26 | 調整：格子與項目皆可開啟浮動功能列；新增輸入半寬且控制列浮動；移除日期欄頭副文字；時間置中；現在格改用提醒色 |
| v0.8 | 2026-06-26 | 修正：點擊「新增」後，新增輸入框與所有新增操作皆浮動於格子最上方，不影響格子尺寸 |
| v0.9 | 2026-06-26 | 調整：移除本機暫存 fallback；Supabase 為唯一資料來源；同步完成以綠燈顯示「同步雲端」 |
| v0.10 | 2026-06-27 | 調整：移除格內「＋新增」文字，點擊格子背景直接新增；設定同步改為更新 `app_settings.id=1`；補充既有 Supabase 專案修正 SQL |
| v0.11 | 2026-06-27 | 調整：日期欄頭改為 `MM/DD(週)`；每日欄寬依最長待辦文字自動貼齊；新增事項支援常用項目與其他文字輸入 |
| v0.12 | 2026-06-27 | 調整：手機版設定改為浮動面板；手機主畫面只保留標題／設定、週日期與表格；週切換、日期跳轉與同步狀態移入手機設定面板 |
| v0.13 | 2026-06-27 | 調整：手機週切換移到設定左側符號按鈕；停用左右滑邊界自動切換；新增「今／週」顯示模式與 `view_mode` 設定 |
| v0.14 | 2026-06-27 | 調整：桌機週切換移到設定左側符號按鈕；設定面板改浮動；「今／週」右側新增上一日／下一日；隱藏待辦項目左側拖曳符號 |
| v0.15 | 2026-06-27 | 調整：常用設定可排序並同步；今模式功能列最上層浮動；手機日期列同列顯示模式切換；手機週模式垂直可見整週；桌機今切週欄寬不驟縮 |
| v0.16 | 2026-06-27 | 調整：點格子與待辦項目皆可先開啟含「新增」的浮動功能列；桌機週模式左右按鈕切換上一週／下一週完整週 |

---

## 15. UI Theme 與每日時段設定補充

> 補充日期：2026-06-26  
> 目的：加入可切換的整體外觀 theme，以及可調整的每日時間範圍與間隔設定。

### 15.1 Theme 模版選擇

`index.html` 需要提供 10 種經典配色 theme 可供選擇。深色模式為預設值，使用者選擇其他 theme 後需自動記憶，下次開啟仍套用上次選擇。

| theme_id | 顯示名稱 | 說明 | 預設 |
|----------|----------|------|------|
| `dark` | 深色模式 | 深色漸層背景，適合長時間查看；沿用目前主視覺。 | 是 |
| `midnight` | 午夜藍 | 更冷調的深藍背景，降低亮度與對比刺激。 | 否 |
| `forest` | 森林綠 | 深綠 / 青綠色系，適合柔和專注感。 | 否 |
| `sunrise` | 日出暖色 | 暖色淺背景，保留清楚的任務卡片與時間線。 | 否 |
| `minimal` | 極簡灰白 | 淺色、低裝飾、適合列印或辦公室環境。 | 否 |
| `classic_blue` | 經典藍白 | 傳統辦公軟體風格，白底、藍色主按鈕與清楚格線。 | 否 |
| `nord` | Nord 冷灰 | 低飽和冷灰藍配色，視覺柔和且適合長時間使用。 | 否 |
| `dracula` | Dracula 紫黑 | 經典深色開發者配色，紫黑底搭配粉紫與青綠強調。 | 否 |
| `solarized` | Solarized | 經典低對比閱讀配色，適合淺色與暗色之間的平衡感。 | 否 |
| `high_contrast` | 高對比 | 深底高亮文字與強邊框，適合戶外或視覺辨識需求。 | 否 |

#### Theme 行為

- 初次開啟時使用 `dark`。
- 使用者切換 theme 後，將選擇同步寫入 Supabase `app_settings.theme_id`。
- 下次開啟頁面時，讀取 Supabase `app_settings.theme_id` 並套用。
- 若讀到不存在的 theme_id，回復為 `dark`。
- Theme 應影響：頁面背景、主要文字、次要文字、格線、任務卡片、今日欄位、目前時間列、重要任務標籤、按鈕與設定面板。
- Theme 主要定義為「配色 theme」，不改變核心週曆操作邏輯；若未來要做不同版面佈局，可另列為 v2。

#### Theme UI

- 在設定面板中提供 theme 選擇控制。
- Theme 控制採下拉式選單呈現。
- 目前選中的 theme 需透過原生選取狀態清楚標示。

### 15.2 每日時段設定

每日時間軸需可由使用者調整，至少包含下列欄位：

| 設定欄位 | 欄位名稱 | 預設值 | 範例 | 說明 |
|----------|----------|--------|------|------|
| `day_start` | 起始時間 | `06:00` | `05:00`、`06:00` | 每日時間格的第一個時間點。 |
| `day_end` | 結束時間 | `22:00` | `22:00`、`23:00` | 每日時間格的最後時間範圍結束點。 |
| `interval_minutes` | 間隔時間 | `30` | `10`、`15`、`30`、`60` | 時間格切分間隔，單位為分鐘。 |

#### 時段設定行為

- 使用者可在設定面板調整「起始時間」、「結束時間」、「間隔時間」。
- 起始時間必須早於結束時間。
- 間隔時間可提供 `10`、`15`、`30`、`60` 分鐘選項。
- 時間軸需依設定即時重繪，例如：
  - `06:00` - `22:00`，間隔 `30` 分鐘。
  - `05:00` - `23:00`，間隔 `10` 分鐘。
- 設定需同步到 Supabase 的 `app_settings`，並可作為全域設定讀取。
- 若 Supabase 讀取失敗，使用預設值：`06:00`、`22:00`、`30`、`dark`、`week`。

#### 設定面板建議呈現

```
外觀 Theme
[深色模式 v]

每日時段
起始時間  [06:00]
結束時間  [22:00]
間隔時間  [30 分鐘 v]
文字大小  [中 v]

常用設定
[輸入常用項目] [新增]
[會議] [刪除]
```

### 15.3 `app_settings` 資料欄位補充

若要記憶 theme，可在 `app_settings` 增加欄位：

| 欄位 | 型別 | 預設值 | 說明 |
|------|------|--------|------|
| `theme_id` | `text` | `'dark'` | 目前使用的全域 theme；前端以 Supabase `app_settings` 為準，不使用本機暫存。 |
| `common_items` | `jsonb` | `[]` | 常用待辦項目文字陣列，供每筆待辦的「常用」下拉選單套用。 |
| `font_size` | `text` | `'medium'` | 待辦項目文字大小設定，允許 `small`、`medium`、`large`、`xlarge`。 |
| `view_mode` | `text` | `'week'` | 日期區間右側「今／週」顯示模式，允許 `today`、`week`。 |

建議允許值：`dark`、`midnight`、`forest`、`sunrise`、`minimal`、`classic_blue`、`nord`、`dracula`、`solarized`、`high_contrast`。

---

## 16. 手機與電腦執行確認

### 16.1 電腦瀏覽器

- 目標瀏覽器：Chrome、Edge、Safari、Firefox 最新穩定版。
- 桌機寬度 ≥1024px 時，預設顯示完整 7 天週視圖。
- 滑鼠可操作：新增、編輯、刪除、勾選、拖曳排序、跨格拖曳、日期欄頭選單、設定面板。
- 若視窗寬度不足，週欄位可橫向捲動，時間欄保持易讀。

### 16.2 手機瀏覽器

- 目標裝置：iPhone 12 Pro Max Safari。
- 手機寬度 <768px 時，週欄位採橫向捲動或單日聚焦式可視範圍，避免 7 欄全部擠在同一畫面。
- 所有可點擊元件高度或觸控範圍需 ≥44px。
- 日期欄頭操作使用底部 Action Sheet，不使用過小的桌機下拉選單。
- 空白格新增、事項編輯、勾選、刪除、顏色標籤、設定面板皆需可用觸控完成。
- 手機拖曳容易與頁面捲動衝突；v1 需提供替代操作，例如長按進入移動模式，或用選單改日期 / 時段。

### 16.3 iPhone Safari 加到主畫面

- 網頁部署到 GitHub Pages 後，可在 iPhone Safari 使用「加入主畫面」建立桌面圖示。
- v1 可先作為一般網頁捷徑使用；若要更像 App，建議補上 `manifest.webmanifest`、`apple-touch-icon`、`theme-color`、`viewport` 與 iOS 相關 meta 標籤。
- 需提供 `apple-touch-icon`，建議至少準備 iPhone 用 `180x180` PNG，避免加入主畫面時只顯示網頁縮圖。
- 建議加入 `apple-mobile-web-app-capable=yes` 與 `apple-mobile-web-app-title`，讓從主畫面開啟時更接近 App 體驗。
- `viewport` 建議使用 `width=device-width, initial-scale=1`；不要禁止縮放，避免輸入與可讀性問題。
- 未導入 Service Worker 時，加入主畫面後仍需要網路才能完整使用 Supabase 同步。
- iOS Safari 的獨立視窗高度與網址列行為不同，UI 需使用安全區域與動態視窗高度處理，避免底部按鈕被 Home Indicator 遮住。

---

若規格無誤，回覆「**可以開始實作**」即可進行 `index.html` 開發，並依本 SPEC 於 Supabase 雲端建立資料表。
