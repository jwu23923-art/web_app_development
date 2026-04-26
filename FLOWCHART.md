# 系統流程圖 (FLOWCHART.md)

本文件使用 Mermaid 語法視覺化「個人記帳本」的使用者操作路徑與系統內部的資料流。

## 1. 使用者流程圖 (User Flow)

描述使用者進入網頁後的主要操作路徑。

```mermaid
flowchart LR
    Start([使用者開啟網頁]) --> Dashboard[首頁：儀表板]
    Dashboard --> ViewStats[查看餘額與分類比例圖]
    Dashboard --> List[查看歷史消費清單]
    
    Dashboard --> Action{執行什麼操作？}
    
    Action -->|新增紀錄| AddForm[填寫收支表單]
    AddForm --> SubmitAdd[確認送出]
    SubmitAdd --> Dashboard
    
    Action -->|編輯紀錄| EditForm[進入編輯頁面]
    EditForm --> Update[儲存變更]
    Update --> Dashboard
    
    Action -->|刪除紀錄| DeleteConfirm{確認刪除？}
    DeleteConfirm -->|是| DoneDelete[系統移除資料]
    DoneDelete --> Dashboard
    DeleteConfirm -->|否| Dashboard
```

---

## 2. 系統序列圖 (Sequence Diagram)

以「新增一筆支出」為例，展示前端、後端與資料庫之間的互動。

```mermaid
sequenceDiagram
    actor User as 使用者
    participant Browser as 瀏覽器
    participant Flask as Flask Route
    participant DB as SQLite 資料庫

    User->>Browser: 填寫金額、類別、備註
    User->>Browser: 點擊「新增紀錄」
    Browser->>Flask: POST /records (資料封包)
    
    Note over Flask: 執行格式檢查與<br/>餘額重新計算邏輯
    
    Flask->>DB: INSERT INTO records (...)
    DB-->>Flask: 回傳執行結果 (Success)
    
    Flask-->>Browser: HTTP 302 Redirect (/index)
    Browser->>Flask: GET /index
    Flask->>DB: SELECT * FROM records (重新撈取)
    DB-->>Flask: 回傳最新資料清單
    Flask-->>Browser: 渲染首頁 (帶入新紀錄)
```

---

## 3. 功能清單對照表

下表列出系統主要功能對應的路由與請求方法。

| 功能名稱 | URL 路徑 | HTTP 方法 | 說明 |
| :--- | :--- | :--- | :--- |
| **首頁儀表板** | `/` | `GET` | 顯示總餘額、統計圖表與近期紀錄 |
| **新增紀錄** | `/record/add` | `POST` | 提交新的收入或支出紀錄 |
| **編輯紀錄頁** | `/record/edit/<id>` | `GET` | 進入特定紀錄的編輯表單 |
| **更新紀錄** | `/record/edit/<id>` | `POST` | 儲存修改後的紀錄內容 |
| **刪除紀錄** | `/record/delete/<id>`| `POST` | 從資料庫中移除特定紀錄 |
| **歷史清單頁** | `/history` | `GET` | (可選) 顯示完整的歷史帳目分頁 |

---
