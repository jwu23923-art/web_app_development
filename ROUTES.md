# 路由設計文件 (ROUTES.md)

本文件規劃「個人記帳本」系統的所有 Flask 路由、HTTP 方法以及對應的前端模板。

## 1. 路由總覽表格

| 功能 | HTTP 方法 | URL 路徑 | 對應模板 | 說明 |
| :--- | :--- | :--- | :--- | :--- |
| **首頁儀表板** | GET | `/` | `index.html` | 顯示總餘額、分類圖表與最近紀錄 |
| **新增紀錄頁** | GET | `/record/add` | `record_form.html` | 顯示新增收支的表單 |
| **建立紀錄行為** | POST | `/record/add` | — | 接收表單、寫入 DB、重導向至首頁 |
| **編輯紀錄頁** | GET | `/record/edit/<int:id>` | `record_form.html` | 顯示特定紀錄的編輯表單 |
| **更新紀錄行為** | POST | `/record/edit/<int:id>` | — | 更新 DB 資料、重導向至首頁 |
| **刪除紀錄行為** | POST | `/record/delete/<int:id>` | — | 刪除資料、重導向至首頁 |

---

## 2. 路由詳細說明

### 首頁 (Dashboard)
- **路徑**: `/`
- **邏輯**:
  1. 呼叫 `Record.get_summary()` 獲取總餘額與收支摘要。
  2. 呼叫 `Record.get_all()` 獲取所有紀錄。
  3. (選用) 依類別過濾資料以繪製圖表。
- **輸出**: 渲染 `index.html`。

### 新增/編輯紀錄 (Record Form)
- **路徑**: `/record/add` 或 `/record/edit/<id>`
- **輸入**: 
  - 表單欄位: `amount`, `type`, `category`, `date`, `note`
- **邏輯**:
  - `GET`: 如果是編輯，先依 `id` 撈取資料填充表單。
  - `POST`: 執行資料驗證 (金額不可為空等) -> 呼叫 `Record.create()` 或 `Record.update()`。
- **輸出**: 成功後重導向至 `/`。

### 刪除紀錄 (Delete Action)
- **路徑**: `/record/delete/<id>`
- **邏輯**: 驗證 ID 存在後呼叫 `Record.delete()`。
- **輸出**: 重導向至 `/`。

---

## 3. Jinja2 模板清單

所有的模板都將繼承 `base.html`。

1. **base.html**: 包含 HTML 骨架、導覽列、導引 CSS/JS 資源。
2. **index.html**: 儀表板頁面，包含總額顯示與歷史列表。
3. **record_form.html**: 共用表單，用於新增與編輯收支。

---

## 4. 路由骨架程式碼

實作於 `app/routes/record.py`。

```python
from flask import Blueprint, render_template, request, redirect, url_for

record_bp = Blueprint('record', __name__)

@record_bp.route('/')
def index():
    """顯示儀表板：總餘額、紀錄列表與統計圖表"""
    pass

@record_bp.route('/record/add', methods=['GET', 'POST'])
def add_record():
    """新增紀錄：GET 顯示表單，POST 儲存資料"""
    pass

@record_bp.route('/record/edit/<int:id>', methods=['GET', 'POST'])
def edit_record(id):
    """編輯紀錄：GET 顯示舊資料表單，POST 更新資料"""
    pass

@record_bp.route('/record/delete/<int:id>', methods=['POST'])
def delete_record(id):
    """刪除紀錄：僅接收 POST 請求以確保安全"""
    pass
```

---
