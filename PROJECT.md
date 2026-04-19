# 專案架構文件

此文件幫助日後快速定位程式碼區段，供修改時參考。

## 檔案總覽

`index.html` 為單一 HTML 檔案，由三大區段組成：

1. **`<style>` 區** — CSS 樣式
2. **`<body>` 區** — HTML 結構（5 個 tab 面板）
3. **`<script>` 區** — JavaScript 邏輯

## HTML 結構地圖

```
<body>
├── <header>                  頁首綠色標題列
├── <div class="tabs">         5 個頁籤按鈕
│   ├── 員工 / 每日 / 月薪 / 匯出 / 設定
│
├── <div id="panel-employees"> 員工管理面板
│   ├── 新增/編輯員工表單
│   └── 員工列表
│
├── <div id="panel-daily">     每日記錄面板
│   ├── 日期 + 員工 + 計酬方式切換
│   ├── 論件計酬表單 (id="pieceCard")
│   ├── 計時計酬表單 (id="hourCard")
│   ├── 當日記錄列表
│   └── 整月加總表格
│
├── <div id="panel-calc">      月薪計算面板
│   ├── 月份 + 員工 + 出勤/加班/獎金輸入
│   ├── 計算預覽
│   └── 本月記錄列表
│
├── <div id="panel-export">    匯出面板
│   ├── Excel 匯出
│   ├── 統計摘要
│   └── 資料備份/還原/清除
│
└── <div id="panel-setup">     設定面板
    ├── LIFF ID 設定
    ├── 佈署教學
    └── 勞健保費率設定
```

## JavaScript 函式地圖

### 資料儲存 (STORE 區)
- `STORE` — localStorage key 常數
- `load(key, def)` / `save(key, val)` — 讀寫 localStorage
- 全域變數：`employees`、`records`、`dailyEntries`、`rates`

### 頁籤切換
- `.tab` 的 click 事件監聽器 — 切換面板並觸發對應 refresh 函式

### 員工管理
- `refreshEmployeeList()` — 重新渲染員工列表
- `saveEmployee()` — 儲存/更新員工
- `editEmployee(id)` / `deleteEmployee(id)` / `clearEmpForm()`

### 每日記錄
- `setDailyMode(mode)` — 切換論件/計時模式
- `updatePiecePreview()` — 論件計酬即時計算小計
- `calcSpanMins(start, end)` — 計算時段總分鐘數（支援跨日）
- `calcHours(start, end, breakMin)` — 計算扣除休息後的實際時數
- `setBreak(min)` — 快速設定休息時間按鈕
- `updateHourPreview()` — 計時計酬即時計算顯示
- `addPieceEntry()` / `addHourEntry()` — 儲存每日記錄
- `refreshDailyEntries()` — 重新渲染當日記錄清單
- `refreshMonthlyDaily()` — 重新渲染整月加總表格
- `deleteDailyEntry(id)` — 刪除記錄

### 月薪計算
- `refreshEmpDropdown()` — 更新員工下拉選單（calc/daily/filter 三處）
- `refreshProductList()` — 更新品名自動完成清單
- `computeSalary(emp, input)` — **核心薪資計算邏輯**
- `gatherCalcInput()` — 從表單蒐集輸入
- `calculateAndPreview()` / `saveSalaryRecord()` / `refreshRecords()` / `deleteRecord()`

### Excel 匯出
- `exportToExcel()` — 產生多工作表 Excel
  - 月薪摘要表
  - 月薪明細表
  - 每日明細表
  - 每日摘要（員工 × 日期）
  - 員工月總
- `updateSummary()` — 畫面上的統計表格

### 備份/還原/清除
- `backupData()` — 匯出 JSON
- `restoreData(event)` — 匯入 JSON
- `clearAllData()` — 清除所有資料

### LINE LIFF
- `initLiff()` — 初始化 LIFF SDK，取得使用者資訊
- `saveLiffId()` — 儲存 LIFF ID 並重新載入
- `saveRates()` — 儲存勞健保費率

### 工具
- `formatMoney(n)` — 數字千分位格式化
- `escapeHtml(s)` — HTML 特殊字元轉義
- `init()` — 頁面啟動入口

## 資料模型

### Employee (員工)
```js
{
  _id: 'emp_xxx',       // 系統唯一 ID
  id: 'E001',           // 員工編號 (可選)
  name: '王小明',
  title: '業務專員',
  hireDate: '2025-01-15',
  baseSalary: 30000,    // 月薪
  hourlyRate: 125,      // 時薪 (加班用)
  note: ''
}
```

### SalaryRecord (月薪記錄)
```js
{
  _id: 'rec_xxx',
  month: '2026-04',
  empId, empName, empNo, empTitle,
  workDays, leaveDays,
  ot1, ot2,             // 加班時數
  bonus, otherDeduct,
  laborIns, healthIns,  // 勞健保
  base, otPay1, otPay2, // 計算結果
  leaveDeduct, gross, deductions, net,
  note, createdAt
}
```

### DailyEntry (每日記錄)
```js
// 論件
{
  _id, date, empId, empName, empNo,
  type: 'piece',
  name: '品名', qty, price, subtotal,
  note, createdAt
}

// 計時
{
  _id, date, empId, empName, empNo,
  type: 'hour',
  start: '09:00', end: '18:00', breakMin: 60,
  hours, rate, subtotal,
  note, createdAt
}
```

## 常見修改位置速查

| 想修改 | 檔案位置 |
|--------|----------|
| 換顏色主題 | `<style>` 區開頭的 `#06C755` (LINE 綠) 全域替換 |
| 加員工欄位 | HTML: `panel-employees` 的 form；JS: `saveEmployee`、`editEmployee` |
| 改勞健保算法 | `computeSalary` 函式內 |
| 改加班倍率 | `computeSalary` 中的 1.34 / 1.67 |
| 加 Excel 新工作表 | `exportToExcel` 函式內 |
| 改 LINE 連動 | `initLiff` 函式 |
| 加外部 API (Firebase 等) | 需大幅改寫 `load/save` 為非同步 |

## 擴充建議

- **雲端同步**：可接 Firebase Firestore / Supabase，讓多裝置共用資料
- **權限控制**：依 LINE User ID 區分管理員/員工檢視範圍
- **PDF 薪資單**：可加入 jsPDF，從月薪記錄產生 PDF
- **圖表**：用 Chart.js 顯示月收入趨勢圖
- **多語言**：抽取文案為 i18n 物件

有任何修改需求都可直接描述，會依此架構定位修改。
