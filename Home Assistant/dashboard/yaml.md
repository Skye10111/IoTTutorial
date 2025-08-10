${\text{{\color{red}一旦通過 YAML 接管 UI 的控制，HA 的介面將不再提供修改儀表板的功能，新增的實體也不會自動出現在此 UI 中。}}}$

# 使用 YAML 修改「總覽儀表板」
- 要更改總覽儀表板，請在 HA 的配置目錄 (`/config`) 中：
  - 創建一個新的文件 `ui-lovelace.yaml`。
  - 在 `configuration.yaml` 文件中新增以下部分。
    ```
    lovelace:
      mode: yaml
    ```
  - 然後重新啟動 Home Assistant。

### 建立 `ui-lovelace.yaml` 的初始內容
- 從 UI 中**複製貼上 “原始配置”（Raw Configuration）**，這樣您的手動配置就會與現有的 UI 配置保持一致。
- 獲取現有儀表板的原始配置
  ```text
  1. 在側邊欄中選擇「總覽」儀表板。
  
  2. 點擊右上角的 pencil 按鈕 --> ⋮ 按鈕 --> 「以 YAML 編輯」按鈕

  3. 您將看到目前儀表板的配置。將其複製貼上到 ui-lovelace.yaml 中。
  ```
- 修改 `ui-lovelace.yaml` 後，無需重新啟動 HA 或刷新頁面。只需點擊 UI 頂部菜單中的刷新按鈕即可。

 ### 恢復到 UI 編輯模式
如果您希望恢復使用 UI 來編輯儀表板，請執行以下步驟：
```
1. 從 configuration.yaml 中移除 lovelace 部分。

2. 將 ui-lovelace.yaml 文件的內容複製到 Home Assistant 的「以 YAML 編輯」中。

3. 重新啟動 Home Assistant。
```


# 使用 YAML 添加多個儀表板
除了修改單一總覽儀表板，您還可以使用 YAML 定義**多個儀表板 (每個儀表板都有對應的 YAML 文件)**。<br/>
在 `configuration.yaml` 文件中新增以下部分：
```
lovelace:
  mode: yaml                       # 主儀表板 (main dashboard) 的模式，可以是 yaml 或 storage (用 UI managed)
  resources:                       # 使用的外部資源，例如：JS 卡片文件、CSS 文件 (僅 yaml mode 下可用)
    - url: /local/myCustomCard.js      # 資源的 URL 路徑，可以是本地文件或外部 URL
      type: module                     # 資源的類型，可以是 module (為 JS 時) 或 css (為 CSS 時）。
    - url: /local/myWebfont.css
      type: css
  dashboards:                     # 用於定義多個 Lovelace 儀表板
    lovelace-generated:               # 儀表板的名稱，必須包含 hyphen (-)
      mode: yaml                          # 此儀表板使用 YAML mode
      filename: notexist.yaml             # 配置檔名 (在 /config 目錄下面)
      title: Generated                    # 此儀表板的標題，會顯示在 sidebar 中
      icon: mdi:tools                     # 為儀表板分配圖標，使用的是 Material Design Icons (MDI) 的名稱 (如 mdi:tools)
      show_in_sidebar: true               # 是否在左側 sidebar 顯示此儀表板 (true 或 false) 
      require_admin: true                 # 是否僅供 admin 訪問 (true 或 false) 
    lovelace-hidden:                  # 儀表板名稱
      mode: yaml
      title: hidden
      show_in_sidebar: false
      filename: hidden.yaml
```
當您的主儀表板 (main dashborad，即**總覽儀表板**) 配置了 UI 時，您也可以新增 YAML 儀表板：
```
lovelace:
  mode: storage                   # 主儀表板為 storage 模式 (使用 UI managed)
  dashboards:                     # 用於定義多個 Lovelace 儀表板
    lovelace-yaml:
      mode: yaml
      title: YAML
      icon: mdi:script
      show_in_sidebar: true
      filename: dashboards.yaml
```

# 儀表板的 YAML 文件
每個儀表板都有對應的 YAML 文件，且都放在 HA 專案目錄的 `/config` 下面。<br/>
以下是使儀表板正常工作所需的最小配置：
```
views:                      # 一個列表，每個項目代表一個儀表板的視圖 (每個視圖代表一個頁面)
  - title: Example              # 視圖的標題，會顯示在 HA 網頁上的視圖名稱
    cards:                      # 該視圖中顯示的卡片內容 (一個列表，每個項目代表一個卡片)
      - type: markdown
        title: Dashboard
        content: >
          Welcome to your **dashboard**.
```
稍微進階一點的例子：
```
views:
  - title: Example            # 第 1 個視圖的標題
    path: example             # 定義視圖的唯一 URL 路徑 (如 /lovelace/example) 方便用戶通過 URL 訪問特定視圖。
    theme: dark-mode          # 視圖使用的主題 (需事先在 HA 的前端中定義)
    cards:
      - type: entity-filter       # entity-filter 卡片，會顯示符合特定 state 過濾條件的實體列表的卡片
        entities:                 # 需要過濾的實體列表（如設備追蹤器、燈光等）    
          - device_tracker.paulus         # 實體 ID 為 device_tracker.paulus 的實體
          - device_tracker.anne_there     # 實體 ID 為 device_tracker.paulus 的實體
        state_filter:             # 指定實體的 state (i.e. 該實體當前狀態值) 的過濾條件
          - 'home'                        # 狀態值為 'home' 時
        card:                     # 定義如何顯示過濾後的實體
          type: glance                    # 使用 glance 卡片的形式呈現
          title: People that are home     # 卡片的名稱
      - type: picture-entity      # picture-entity 卡片，會顯示帶圖片的實體卡片
        image: https://.../img.png        # 實體卡片的圖片 URL (圖片作為背景，並在圖片上方或旁邊顯示實體信息)
        entity: light.bed_light           # 實體的實體 ID (這個實體相關的信息會在卡片中顯示)

  - icon: mdi:home-assistant  # 第 2 個視圖的圖標
    title: Second view        # 第 2 個視圖的標題
    cards:
      - type: entities            # entities 卡片，會顯示顯示多個實體的狀態信息
        title: Example            # 卡片的標題
        entities:                 # 實體列表 (每個實體可以是簡單的實體 ID 或帶有額外屬性)
          - light.kitchen
          - switch.ac
          - entity: light.living_room # 實體 ID
            name: LR Lights           # 自定義顯示的名稱（可選）

      - type: markdown            # entity-filter 卡片，會顯示 Markdown 格式的文字
        title: Dashboard          # 卡片的標題
        content: >                    # Markdown 語法的文字內容 (">" 會把多行文字合併成一行，行末的換行符替換為空格)
          Welcome to your **dashboard**.
```
