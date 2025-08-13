# 簡介
- 每個儀表板均由卡片所組成。
- 卡片類型多種多樣，每種都有各自的配置選項。它們可以根據功能進行分類。
  - 根據特定設備類型或服務：<br/>
    <table>
      <tr>
        <td>警報 (alarm)</td> <td>燈光 (light)</td> <td>加濕器 (humidifier)</td> <td>恆溫器 (thermostat)</td>
      </tr>
      <tr>
        <td>植物狀態 (plant status)</td> <td>媒體控制 (media control)</td> <td>天氣預報 (weather forecast)</td> <td>待辦事項清單 (to-do list)</td>
      </tr>
      <tr>
        <td>地圖 (map)</td> <td>日誌 (logbook)</td> <td>日曆 (calendar)</td> <td></td>
      </tr>
    </table>

  - 用於分組其他卡片：<br/>
    <table>
      <tr>
        <td>垂直堆疊 (vertical stack)</td> <td>水平堆疊 (horizontal stack)</td> <td>網格 (grid)</td>
      </tr>
    </table>
  - 用於邏輯功能：<br/>
    <table>
      <tr>
        <td>條件卡片 (conditional)</td> <td>實體篩選 (entity filter)</td>
      </tr>
    </table>
  - 顯示通用數據：<br/>
    <table>
       <tr>
        <td>感測器 (sensor)</td> <td>歷史圖表 (history graph)</td> <td>統計 (statistic)</td> <td>統計圖表 (statistics graph)</td>
      </tr>
      <tr>
        <td>能源 (energy)</td> <td>儀表 (gauge)</td> <td>網頁 (webpage)</td> <td></td>
      </tr>
    </table>
  - 控制設備和實體：<br/>
    <table>
      <tr>
         <td>按鈕 (button)</td> <td>實體 (entity)</td>
      </tr>
    </table>
  - 顯示數據並控制實體：<br/>
    <table>
      <tr>
        <td>區域 (area)</td> <td>圖片元素 (picture elements)</td> <td>圖片概覽 (picture glance)</td>
      </tr>
    </table>

# 在儀表板中新增卡片
卡片可以直接**從視圖 (view) 中添加**，或者**從裝置 (device) 頁面添加**。
### 從視圖 (view) 添加卡片
```
1. 進入視圖 --> 點擊視圖右下角「新增面板（Add card）」。

2. 新增卡片 (2 種方法) 
   - 方法 1：以面板搜尋（By card type）
     - 如果已經知道要使用的卡片類型，請選擇「以面板搜尋（By card type）」，瀏覽可用卡片的列表。
   - 方法 2：以實體搜尋（By entity）
     - 如果想為多個實體 (entity) 一次性新增卡片，請選擇「按實體（By entity）」。
     - 從列表中選擇要顯示的實體 (可複選)，然後點擊「繼續（Continue）」。

3. 定義條件（可選）
   - 如果希望此卡片僅對特定使用者可見，或僅在某些條件下顯示，可以在配置中定義這些條件。
   - 步驟：
     (1) 在卡片配置中，切換到 「可視度（Visibility）」 標籤，點擊「新增觸發判斷（Add condition）」。
         -「可視度」標籤無法在嵌套卡片（如 Vertical Stack、Horizontal Stack 和 Grid Card）內使用。
     (2) 選擇您需要的條件類型（如下），並輸入相關參數。
         - 實體數值狀態 (Numeric state)：測試 entity state 是否符合 threshold。
         - 實體狀態 (State)：測試實體是否具有指定狀態。
         - 螢幕 (Screen)：根據螢幕尺寸指定卡片可視度。UI 中提供了一些螢幕尺寸預設，但您可在 YAML 中使用任何想要的 CSS media query。
         - 使用者 (User)：指定哪些使用者可以看到卡片。
         - 且 (And)：指定必須滿足所有條件。
         - 或 (Or)：指定必須滿足至少一個條件。

4. 調整卡片佈局（僅 Sections 視圖）
   - 如果是將卡片添加到「區塊式 (Sections) 視圖」，可以在「版面配置 （layout)」標籤中調整卡片的大小。

5. 自定義卡片內容
   - 定義卡片的行為 (action，見下面 card action 章節)，如按鈕動作。
   - 設置卡片的 Header 與 Footer (見下面 Header & Footer 章節)。
   - 自定義功能（部分卡片可能不支持這些元素，請參考特定卡片類型的文檔）。

6. 完成配置後，點擊「儲存」，新增到儀表板。
```

### 從裝置 (device) 頁面添加
此方法適用於您已經在裝置頁面上，並希望直接從那裡創建卡片。
```
1. 設定 --> 裝置與服務（Devices & services）--> 頂部導覽列的「裝置（Devices）」

2. 點擊感興趣的裝置，進到該裝置的頁面。

3. 找到您感興趣的區域（例如感測器或事件），然後選擇「新增至儀表板（Add to Dashboard）」

4. 編輯卡片（可選）
   - 如果需要修改卡片配置，打開添加卡片的視圖，自定義卡片的行為、標題、頁腳或其他功能（具體功能支持需參考卡片類型文檔）。
```

# 卡片行為 (card action) 
部分卡片支援 Actions 功能，這些功能可以讓使用者設定在卡片內的某個物件上「點擊 (tap)」或「長按 (hold)」時會發生什麼操作。以下是支援 Actions 功能的卡片列表，以及它們的基本功能介紹：
| 卡片 | 說明 |
| ---- | --- |
| **Button** (按鈕卡片) | 顯示一個按鈕，通常用於觸發某些操作或執行服務。 |
| **Entities** (實體列表卡片) | 顯示一組實體的狀態，可以為每個實體設置點擊或長按動作。 |
| **Gauge** (儀表卡片) | 顯示數值的儀表式視圖，例如溫度或電量。可設定點擊動作。 |
| **Glance** (概覽卡片) | 以簡化方式顯示多個實體的狀態，支持點擊或長按查看更多資訊。 |
| **Light** (燈光卡片) | 專門用於控制燈光，支援點擊切換燈光開關或顯示詳細控制。 |
| **Picture** (圖片卡片) | 顯示一張圖片，並可以設定點擊或長按動作，比如打開 URL。 |
| **Picture Element** (圖片元素卡片) | 將互動元素（如按鈕或文字）添加到圖片上。每個元素都可設定 Actions。 |
| **Picture Entity** (圖片實體卡片) | 顯示實體的圖片（例如相機快照），並支持點擊查看更多資訊或執行操作。 |
| **Picture Glance** (圖片概覽卡片) | 顯示圖片並附帶多個實體的狀態，支援點擊或長按動作。 |
| **Tile** (磁磚卡片) | 以簡潔的磁磚樣式顯示實體狀態，可用於快速操作。 |
| **Weather Forecast** (天氣預報卡片) | 顯示天氣預報，支持點擊動作，例如導航至更詳細的天氣資訊。 |

### Tap action 
- tap_action 是用於定義當使用者「**點擊**」卡片上的物件時，會執行什麼操作的配置。
- 預設 tap action 為 `toggle`，但如果實體不能被 toggle，則默認為 `more-info`。
```
tap_action:  
  action: toggle                                # 點擊卡片物件時執行的動作，可為：more-info、toggle、perform-action、navigate、url、assist、none
  navigation_path: /lovelace/0/                 # (可選) 當 action 為 navigate 時，點擊要導航到的頁面路徑
  navigation_replace: false                     # (可選) 當 action 為 navigate 時，是否在歷史記錄中用新頁面替換當前頁面（ boolean 值）
  url_path: https://example.com                 # (可選) 當 action 為 url 時，點擊要打開的 URL
  perform_action: media_player.media_play_pause # (可選) 當 action 為 perform-action 時，點擊要執行的服務
  data:                                         # (可選) 當 action 為 perform-action 時，執行服務時的額外數據
    brightness: 100
  target:                                       # (可選) 當 action 為 perform-action 時，指定行動的目標(實體)
    entity_id: media_player.living_room
  pipeline_id: preferred                        # (可選) 當 action 為 assist 時，語音助理使用的管道 ID，可為 last_used、preferred 或管道 ID
  start_listening: true                         # (可選) 當 action 為 assist 時，打開助理窗口 (assist dialog) 時是否立即開始聆聽語音命令。
  entity: light.kitchen                         # (可選) 當 action 為 more-info 時，點擊顯示另一個不同的實體的詳細資訊（而不是卡片本身綁定的實體） 
  confirmation:                                 # (可選) 顯示確認對話框以確認操作，可以是 boolean 值或更詳細的配置。
    text: "確定要切換燈光嗎？"
```
| 動作類型 | 說明 |
| ------- | --- |
| `more-info` | 顯示實體的詳細資訊彈窗，例如狀態、屬性等。 |
| `toggle` | 切換實體的狀態，例如開啟或關閉燈光。 |
| `perform-action` | 呼叫 HA 的服務來執行特定操作，例如暫停或播放媒體播放器。<br/>可額外使用 `data` 和 `target` 來指定服務的詳細參數。 |
| `navigate` | 導航到 HA 的另一個視圖或頁面。<br/>配置選項：`navigation_path` 和 `navigation_replace`。 |
| `url` | 打開指定的 URL（例如外部網站或文件）。<br/>配置選項：`url_path`。 |
| `assist` | 打開 HA 的語音助理窗口，並執行管道 (`pipeline_id`) 或開始聆聽語音命令 (`start_listening`)。 |
| `none` | 不執行任何操作，適合用於純展示的卡片。 |
例如，在 button card 上面設定 tap 和 hold 兩個 action：
```
cards:  
  - name: Watch Netflix  
    entity: script.netflix  
    type: button  
    tap_action:  
      action: toggle  
    hold_action:  
      action: more-info 
```

### Hold action
- `hold_action` 指的是當使用者「**點擊並長按（持續至少半秒）**」卡片上的物件，並釋放後執行的操作。
- 這個動作只會觸發一次，不會在持續按住期間重複執行。
- 預設 tap action 為 `more-info`。
```
hold_action:  
  action: toggle              # 點擊並長按卡片物件時執行的動作
  <其他配置選項>                # 同上「tap action」的說明
```
可用的 action 類型：`more-info`、`toggle`、`perform-action`、`navigate`、`url`、`assist`、`none`。

### Double Tap action
- `double_tap_action` 是指當使用者「**快速雙擊**」卡片上的物件時執行的動作。
- 預設 tap action 為 `more-info`。
```
double_tap_action:  
  action: toggle             # 雙擊卡片物件時，執行的動作
  <其他配置選項>               # 同上「tap action」的說明
```
可用的 action 類型：`more-info`、`toggle`、`perform-action`、`navigate`、`url`、`assist`、`none`。



# 卡片首尾 ( Header & Footer)

# 卡片介紹
