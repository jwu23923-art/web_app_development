# 系統流程圖：個人食譜收藏夾 (Personal Recipe Collection)

本文件描述使用者在「個人食譜收藏夾」中的操作路徑，以及資料在前端與後端系統間的流轉流程。

## 1. 使用者流程圖 (User Flow)

此圖展示使用者從進入網站到管理食譜的完整生命週期。主要包含：瀏覽列表、新增食譜、查看詳情、編輯與刪除等核心動作。

```mermaid
flowchart LR
    Start([使用者開啟網頁]) --> Home[首頁：食譜清單]
    
    Home --> Action{要執行什麼操作？}
    
    %% 新增流程
    Action -->|點擊新增| AddForm[填寫食譜表單]
    AddForm --> AddSave[點擊儲存]
    AddSave --> Home
    
    %% 查看與管理
    Action -->|點擊食譜標題| Detail[食譜詳情頁]
    Detail --> Manage{管理動作}
    
    Manage -->|編輯| EditForm[編輯食譜表單]
    EditForm --> EditSave[點擊更新]
    EditSave --> Detail
    
    Manage -->|刪除| DeletePop[確認刪除視窗]
    DeletePop -->|確認| DeleteConfirm[執行刪除]
    DeleteConfirm --> Home
    DeletePop -->|取消| Detail
    
    %% 篩選流程
    Action -->|選擇分類標籤| Filter[過濾清單]
    Filter --> Home
```

## 2. 系統序列圖 (Sequence Diagram)

此序列圖描述以「**新增一個新食譜**」為例的系統運作細節，涵蓋從前端輸入到後端資料庫存取的過程。

```mermaid
sequenceDiagram
    actor User as 使用者
    participant Browser as 瀏覽器 (HTML/JS)
    participant Flask as Flask Route (Controller)
    participant Model as Database Model
    participant DB as SQLite 資料庫

    User->>Browser: 1. 填寫食譜名稱、食材、步驟與分類
    User->>Browser: 2. 點擊「儲存食譜」
    Browser->>Flask: 3. POST /recipe/new (傳送表單資料)
    Note over Flask: 驗證資料是否完整
    Flask->>Model: 4. 呼叫食譜新增函式 (add_recipe)
    Model->>DB: 5. INSERT INTO recipes (name, ingredients, steps, category)
    DB-->>Model: 6. 寫入成功
    Model-->>Flask: 7. 回傳新食譜 ID
    Flask-->>Browser: 8. HTTP 302 Redirect to / (重導向回首頁)
    Browser->>User: 9. 顯示更新後的食譜列表
```

## 3. 功能清單與路由對照表

以下為本專案的核心路由規劃，將作為後續開發路由 (`/api-design`) 的依據。

| 功能名稱 | URL 路徑 | HTTP 方法 | 金字塔模板 (Jinja2) | 說明 |
|----------|----------|-----------|--------------------|------|
| **食譜首頁** | `/` | GET | `index.html` | 顯示所有食譜，支援分類顯示 |
| **新增食譜頁** | `/recipe/new` | GET | `recipe_form.html` | 顯示空白表單供填寫 |
| **執行新增** | `/recipe/new` | POST | 無 (導向 `/`) | 接收資料並存入資料庫 |
| **食譜詳情頁** | `/recipe/<id>` | GET | `detail.html` | 顯示單一食譜的詳細做法 |
| **編輯食譜頁** | `/recipe/<id>/edit` | GET | `recipe_form.html` | 顯示帶有舊資料的表單 |
| **執行更新** | `/recipe/<id>/edit` | POST | 無 (導向 `/recipe/<id>`) | 更新資料庫紀錄 |
| **執行刪除** | `/recipe/<id>/delete`| POST | 無 (導向 `/`) | 刪除資料庫紀錄 |
| **分類篩選** | `/search` | GET | `index.html` | 根據 URL 參數 `category` 進行篩選 |
