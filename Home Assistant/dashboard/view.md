# 簡介
**視圖 (view)** 是儀表板內的「分頁/畫布」 (tab)，例如下面的螢幕截圖為「總覽」儀表板上燈光的視圖。
![image](https://www.home-assistant.io/images/dashboards/dashboard_view-tab.png)

視圖控制畫面的 layout，基本的 3 種視圖佈局為**面板 (Panel)**、**側邊欄 (Sidebar)** 和 **瀑布流式 (Masonry)**。
![image](https://www.home-assistant.io/images/dashboards/layout-types.png)
| 視圖類型 | 說明 |
| ------- | --- |
| **區塊 (Sections，預設)** | 將卡片按照**網格系統**排列，並允許將卡片分組到不同的區塊中。 |
| **瀑布流式 (Masonry)** | 根據卡片的大小，將卡片排列成多行。 |
| **面板式 (Panel)** | 以全寬的方式顯示一張卡片，例如地圖或圖片。 |
| **側邊欄式 (Sidebar)** | 將卡片排列為兩欄，左側為寬欄，右側為窄欄。 |

# 在儀表板中加入視圖
1. `儀表板 --> 右上角鉛筆圖示 --> 進入編輯模式`
2. 在頂部選單欄中，點擊「+」按鈕 (新增畫布)。
3. 設定視圖的相關屬性
   - **版面配置（View Type）**： 選擇視圖的類型。
     - 區塊 (Sections，預設)
     - 瀑布流式 (Masonry)
     - 面板式 (Panel)
     - 側邊欄式 (Sidebar)
   - **標題（Title）**：如果需要視圖的標題，請輸入標題名稱。
   - **圖示（Icon）**：如果需要顯示圖示，請選擇視圖的圖示 (使用 Material 圖示庫)。
     - 如果定義了圖示，則僅顯示圖示，文字僅作為工具提示（tooltip）顯示。
   - **連結（URL）**：如果需要連結到其他視圖，請定義該視圖的 URL (輸入值會成為開啟此畫布的網址路徑的一部分)。
   - **主題（Theme）**： 如果需要使用已定義的主題，請選擇主題。
   - 如果此視圖僅作為子視圖（Subview）使用，請啟用「子視圖」切換按鈕。
   - 如果使用區塊 (section) 視圖，請選擇想要的 column 數 (i.e. 一個橫列最多容納幾個區塊)。
   - 若希望系統填補卡片間的空隙，請啟用「密集區塊排列」選項。
4. 背景設定（Background）
   - 在「背景」分頁中，選擇一張背景圖片並自訂相關的背景配置。
5. 顯示選項設定（Visibility）
   - 新增的視圖預設對所有使用者可見。
   - 如果需要隱藏視圖，可在「可見性」標籤中禁用該視圖。
6. 實體章設定（Badges）
   - 選擇要通過實體章表示的實體。
   - 注意：**側邊欄（Sidebar）和面板（Panel）視圖不支援徽章**。

# 使用 YAML 編輯視圖
### 配置架構概述
主配置文件 (`/config/configuration.yaml`)：
```
homeassistant:         # 主要配置  
  # ... 一些配置

default_config:        # 啟用核心功能  
   
logger:                # 日誌記錄 
  default: info

# 設定儀表板
lovelace:
  mode: storage           # 主儀表板 (總覽儀表板)
  dashboards:             # 在此設定其他儀表板
    lovelace-generated:   # 儀表板 A
      mode: yaml
      title: Generated           # 儀表板標題
      icon: mdi:script
      show_in_sidebar: true
      filename: generated.yaml   # 此儀表板的配置檔 (在 /config 目錄下面)
    lovelace-hidden:      # 儀表板 B
      # ...
```
Generated 儀表板的配置文件 (`/config/generated.yaml`)：
```
views:                  # 為一個列表，每個項目代表一個視圖
  - title: Living room  # Living room 視圖
    # ... 一些視圖設定 (見下面教學)
    badges:                  # 實體章列表 (在該視圖中顯示)
      # ...
    cards:                   # 卡片列表 (在該視圖中顯示)
      # ...
```
### 視圖的配置變數  
| 變數 | 類型 | 必填 | 描述 |  
|-----|-----|------|-----|  
| `views` | list | 必填 | 所有視圖的配置列表。 |  
| `type` | string | 可選（默認 masonry） | 視圖的類型。 |  
| `title` | string | 必填 | 子視圖的標題或名稱。 |  
| `badges` | list | 可選 | 要顯示為實體章的實體 ID 或實體章對象列表。<br/>**注意：徽章在 panal 模式下不顯示。** |  
| `cards` | list | 可選 | 要在此子視圖中顯示的卡片列表。 |  
| `path` | string | 可選 (default: view index) | 用於 URL 的路徑。 |  
| `icon` | string | 可選 | **僅適用於「視圖」，不適用於「子視圖」。** <br/>使用 [Material Design Icons](https://materialdesignicons.com/) 的圖示名稱。 |  
| `background` | map | 可選 | 設置視圖背景的樣式。                                                                          |  
| `theme` | string | 可選 | 設定此視圖及其卡片的主題。 |  
| `visible` | boolean 或 list | 可選 | 隱藏/顯示此視圖分頁，適用於所有用戶或特定用戶。 |  
| `subview` | boolean | 可選 (默認 false) | 將此視圖標記為「子視圖」。 |  
| `back_path` | string | 可選 | **僅適用於「子視圖」。** <br/>點擊返回按鈕時導航到的自訂路徑。  |  

### 視圖的類型
```
- title: Map     # Map 視圖
  type: panel    # 設定 Map 視圖為 panel 類型
  cards:
    - type: map
      entities:
        - device_tracker.demo_paulus
        - zone.home
```

### 視圖的 URL
- 使用支持 navigation 功能的卡片（`navigation_path`），您可以從一個視圖的卡片連結到另一個視圖。
- 這裡提供的字串將附加到 `/lovelace/` 後，形成視圖的完整路徑。
  - 注意：不要在路徑中使用特殊字符。
  - 注意：路徑不能以數字開頭，否則解析器會將路徑視為視圖索引。
- 範例：
  - View configuration
  ```
  - title: Living room
    path: living_room    # 最終路徑為 /lovelace/living_room
  ```
  - Picture card configuration
  ```
  - type: picture
    image: /local/living_room.png
    tap_action:
      action: navigate
      navigation_path: /lovelace/living_room  # 點擊跳轉到 Living room 視圖
  ```

### 視圖圖示（View Icon）
如果您定義了視圖圖示，將顯示圖示而非標題。標題將作為工具提示（tooltip）顯示。
```
- title: Garden
  icon: mdi:flower
```

### 視圖顯示選項（Visible）
- 您可以為整個視圖或特定使用者設置可見性。
- 此配置僅影響分頁（tab）的顯示，透過 URL 路徑仍然可訪問。
- 如果將 `visible` 定義為物件而不是布林值，代表指定條件來顯示視圖分頁。
  - `user`屬性：(字串，必填)
    - 用戶 ID（十六進位唯一值），指定該用戶可以看到視圖分頁。此 ID 可在「用戶配置頁面」中找到。 
```
- title: Ian    
  visible:    
    - user: 581fca7fdc014b8b894519cc531f9a04    
  cards:    
    ...    
- title: Chelsea    
  visible:    
    - user: 6e690cc4e40242d2ab14cf38f1882ee6    
  cards:    
    ...    
- title: Admin    
  visible: db34e025e5c84b70968f6530823b117f    
  cards:    
    ...  
```

### 視圖的主題 (theme)
您可以為視圖及其卡片設置單獨的主題 (theme)，從而使該視圖的外觀與其他視圖不同。
```
- title: Home    
  theme: happy   # 視圖 Home 將使用名為「happy」的主題。
```

### 視圖的背景 (Background)
- 您可以**自訂視圖的背景設置**，為視圖添加背景圖片。或者，**使用主題變數**來統一自訂所有視圖的背景。
- **針對特定視圖的背景設定** 
  ```
  - title: Home 
    background:
      image: /local/background.png   # 背景圖片路徑 (本地路徑或 Web URL)
      opacity: 50                    # 背景圖片透明度，值為 0（完全透明）~ 100（完全不透明）。預設 100
      size: auto                     # auto (預設，原圖大小)、cover (填滿視圖，必要時裁剪圖片), contain (使圖片完全適應視圖，保持寬高比)
      alignment: center              # 定位背景圖片 (預設 center)，可能值：top left, top center, top right, center left, center, center right, bottom left, bottom center, bottom right
      repeat: no-repeat              # 控制背景是否重複，可能值：repeat (背景重複鋪滿視圖)、no-repeat (預設)
      attachment: scroll             # 控制背景圖片的位置是否固定，可能值：scroll (預設，背景隨視圖滾動)、fixed。
  ```  
- **背景主題變數**
  - 使用 theme 來統一自訂所有視圖的背景。使用 CSS 變數 `lovelace-background`。
  - 在此示例中，`lovelace-background` 變數設定了背景圖片的位置、大小、重複方式以及是否固定等屬性。這將應用於所有使用該主題的視圖。
  ```
  # 在 configuration.yaml 中的配置示例  
  frontend:    
    themes:    
      example:    
        lovelace-background: center / cover no-repeat url("/local/background.png") fixed  
  ``` 


# 子視圖（Subview）
- 可以將視圖標記為「Subview」。子視圖**不會顯示**在側邊欄頂部的導航欄中。
  ```
  - title: Map
    subview: true  # 將視圖標記為子視圖
  ``` 
- 子視圖通常用於顯示詳細信息，並可以**通過支持 navigation 操作的卡片從其他視圖中連結到子視圖**。
  - 例如：在一個視圖中顯示幾個恆溫器，並在子視圖中顯示供暖/制冷設備的詳細狀態信息。
- 默認情況下，在子視圖點擊返回按鈕會返回到之前的視圖，但您可以自訂返回路徑（`back_path`）。
  ```
  - title: Map
    subview: true
    back_path: /lovelace/home  # 點擊返回按鈕會到 /lovelace/home 視圖
  ```

# YAML 配置範例
### 視圖範例
```
- title: Living room
  badges:
    - device_tracker.demo_paulus
    - entity: light.ceiling_lights
      name: Ceiling Lights
      icon: mdi:bulb
    - entity: switch.decorative_lights
      image: /local/lights.png
```
### 子視圖範例
```
- title: "Energieprijzen"
  path: "energieprijzen"
  subview: true
  back_path: "/ui-data/climate"
  cards:
    - type: entities
      entities:
        - sensor.today_avg_price
```
