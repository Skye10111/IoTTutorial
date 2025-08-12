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

# 卡片首尾 ( Header & Footer)

# 卡片介紹
