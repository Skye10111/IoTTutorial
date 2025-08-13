# 簡介
實體章是位於視圖頂部的元件，顯示在所有卡片之上。
![image](https://www.home-assistant.io/images/dashboards/badges.png)

# 將實體章新增至您的儀表板
```
1. 設定 --> 儀表板 --> 要新增實體章的視圖 --> 點擊「新增實體章」按鈕

2. 選擇您希望顯示實體章的實體。

3. 配置您的實體章。
   - 可用選項取決於所選的實體。
   - 新增您希望看到的狀態 (state)。
   - 如果需要，可以新增名稱 (name)。

4. 在【互動】(interaction) 部分，您可以定義 tap 的 action。

5. 如果希望此實體章僅對特定使用者或某些條件下可見，請打開【可視度】(Visibility) 標籤以設定條件。
   - 可用條件與 conditional card 中的條件相同。
```

# 實體章的基本 YAML 配置格式
```
views:  
  - title: 主頁  
    path: home
    badges:                     # 實體章列表
      - type: entity            # 實體章種類 (entity、entity-filter)
        # ... 其他實體章配置
      # ...
    cards:  
      # ...
```


# 實體章種類
### Entity badge
Entity badge 可**顯示實體的狀態，並支持操作功能**。
```
type: entity                 # 設定實體章的類型為 entity
entity: light.living_room    # 實體 ID
name: 客廳燈光                 # (可選) 自定義實體名稱，覆蓋實體的原始名稱
icon: mdi:lightbulb          # (可選) 自定義實體圖示，覆蓋實體的原始圖示 
color: "#FFD700"             # (可選) 當實體為 active 時的顏色，可接受顏色代碼或代號。
show_entity_picture: false   # (可選) 如果實體有圖片，是否取代 icon 顯示，預設 false。
show_name: true              # (可選) 是否顯示實體名稱，預設 false。
show_icon: true              # (可選) 是否顯示圖示，預設 true。
show_state: true             # (可選) 是否顯示實體狀態，預設 true。
state_content:               # (可選) string 或 list，要顯示的狀態內容，可為：state（狀態）、last_changed（最後更改時間）、last_updated（最後更新時間）
  - state  
  - last_changed  
tap_action:  
  action: toggle  
hold_action:  
  action: more-info  
double_tap_action:  
  action: none
```

### Entity Filter Badge
- Entity badge 可讓您**定義一個實體列表，並僅在特定狀態下追蹤這些實體**，這非常適合用來顯示未關閉的燈光或僅在某些人居家時顯示他們的列表。
- 範例: 如果客廳的燈光是開啟的，且 John Doe 在家，Bage 會顯示這 2 個實體。
```
type: entity-filter             # 設定實體章的類型為 entity-filter
entities:                       # 要追蹤的實體列表 (當符合條件時，會顯示這些實體)，可以是實體 ID 或實體物件
  - light.living_room  
  - person.john_doe   
conditions:                     # (可選) 要檢查的條件列表
  - entity: light.living_room  
    state: "on"
  - entity: person.john_doe  
    state: "home"
```
每個條件可以包含以下屬性：
| 條件屬性 | `entity` | `state` | `attribute` |`value` |
| ------- | -------- | ------- | ----------- | ------ |
| **說明** | 實體 ID | 要檢查的狀態 | 實體的屬性 | 屬性的值 |

如果將 `entities` 裡面的實體定義為物件（而非實體 ID），可以進行更多自定義和配置：
```
type: entity-filter  
entities:  
  - entity: light.living_room   # 實體 ID
    name: Living Room Light     # (可選) 覆蓋實體的名稱
    icon: mdi:lightbulb         # (可選) 覆蓋實體的 icon 或 entity picture
    conditions:                 # (可選) 要檢查的條件列表，每個條件可以包含屬性 entity、state、attribute、value
      - entity: light.living_room
        state: "on"  
  - entity: person.john_doe
    name: John Doe  
    icon: mdi:account  
    conditions:  
      - entity: person.john_doe  
        state: "home"  
```

# Condition 配置 (在 Entity Filter Badge)
### State（狀態）
- 測試實體是否具有指定的狀態值，至少需要 `state` 或 `state_not` 其中之一。
```
# 範例：顯示所有加熱中的溫控器
type: entity-filter  
entities:  
  - climate.thermostat_living_room  
  - climate.thermostat_bed_room  
conditions:  
  - condition: state   # 需設為 state
    state: heat        # (可選) 實體的 state 或 ID 等於此值，可為 list 或 string
```
```
# 範例：顯示所有未關閉的溫控器
type: entity-filter
entities:
  - climate.thermostat_living_room
  - climate.thermostat_bed_room
conditions:
  - condition: state   # 需設為 state
    state_not: "off"   # (可選) 實體的 state 或 ID 不等於此值，可為 list 或 string
```

### Numeric State（數值狀態）
- 測試實體狀態是否符合閾值。
- 至少需要 `above` 或 `below`，兩者也可同時使用以測試區間值。
```
# 範例：顯示溫度介於 10 至 20 的感應器
type: entity-filter
entities:  
  - sensor.outside_temperature  
  - sensor.living_room_temperature  
  - sensor.bed_room_temperature  
conditions:  
  - condition: numeric_state     # 需設為 numeric_state
    above: 10                    # (可選) 實體 state 或 ID 大於此值。
    below: 20                    # (可選) 實體 state 或 ID 小於此值。
```

### Screen（螢幕大小）
根據螢幕尺寸指定實體的可視度。
```
# 範例： 僅在螢幕寬度大於 1280px 時顯示
type: entity-filter  
entities:  
  - sensor.outside_temperature  
  - sensor.living_room_temperature  
  - sensor.bed_room_temperature  
conditions:  
  - condition: screen                    # 需設為 screen
    media_query: "(min-width: 1280px)"   # css media query，用於檢查允許顯示實體的螢幕大小。
```

### User（使用者）
根據使用者指定實體的可見性。
```
type: entity-filter  
entities:  
  - sensor.outside_temperature  
  - sensor.living_room_temperature  
  - sensor.bed_room_temperature  
conditions:  
  - condition: user                       # 需設為 user
    users:                                # 允許查看實體的使用者 ID 列表 (可在使用者設定頁面中找到）)
      - 581fca7fdc014b8b894519cc531f9a04  
```

### And（邏輯「且」）
指定多個條件必須同時滿足。
```
# 範例：僅顯示溫度大於 0 且使用者為特定 ID 的感應器
type: entity-filter  
entities:  
  - sensor.outside_temperature  
  - sensor.living_room_temperature  
  - sensor.bed_room_temperature  
conditions:  
  - condition: and                        # 需設為 and
    conditions:                           # (可選) 要檢查的條件列表
      - condition: numeric_state  
        above: 0  
      - condition: user  
        users:  
          - 581fca7fdc014b8b894519cc531f9a04  
```

### Or（邏輯「或」）
指定至少一個條件必須滿足。
```
# 範例：顯示溫度大於 0 或使用者為特定 ID 的感應器
type: entity-filter  
entities:  
  - sensor.outside_temperature  
  - sensor.living_room_temperature  
  - sensor.bed_room_temperature  
conditions:  
  - condition: or                        # 需設為 or  
    conditions:                          # (可選) 要檢查的條件列表
      - condition: numeric_state  
        above: 0  
      - condition: user  
        users:  
          - 581fca7fdc014b8b894519cc531f9a04  
```
