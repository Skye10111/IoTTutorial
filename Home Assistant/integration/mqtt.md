# 簡介
| 概念 | 說明 | 功能與用途 |
| --- | ---- | -------- |
| **Integration** | MQTT 整合負責處理 MQTT 消息並解析主題（topic）和 payload。 | 1. 連接 MQTT Broker。<br/>2. 接收與發送 MQTT 消息。<br/>3. 創建 device 和 entity。 |
| **Device** | 例如一個智能燈泡，MQTT 消息主題 `/light/bulb1` 可能代表該設備。 | - 組合相關 entity。 |
| **Entity** | 例如智能燈泡包含 2 個實體 (同時也是 MQTT topic)：<br/>- 開關狀態：`/light/bulb1/state`<br/>- 亮度：`/light/bulb1/brightness` | 1. 監控設備狀態<br/>2. 在界面上顯示數據<br/>3. 通過 service 進行操作（如：開關、調整亮度）。 |
| **Service** | 使用 mqtt.publish 服務發送消息到 `/light/bulb1/state` 主題 (i.e. 實體) 以開關燈泡。 | 1. 控制設備（如開關燈泡、設置溫度）<br/>2. 發送自定義 MQTT 消息到特定主題 |

MQTT 整合的工作流程：
1. **Integration**：MQTT 整合連接到 MQTT Broker，負責接收和發送消息，並解析主題以創建設備和實體。
2. **Device**：整合解析的主題可能代表某些設備。例如，`/light/bulb1` 主題可以代表燈泡設備。
3. **Entity**：設備的屬性（如燈泡的開關狀態和亮度）被定義為實體，這些實體是 HA 中的基本操作單元。
4. **Service**：使用服務（如 `mqtt.publish` 或 `light.turn_on`）控制實體（例如打開燈泡或調整亮度）。

${\text{{\color{red}MQTT Device 和 entity 可以透過 MQTT discovery 或者手動使用 YAML 文件進行配置。}}}$

# 手動配置 YAML 文件
在 `configuration.yaml` 檔中加入 MQTT 裝置，例如：
```
homeassistant:
  name: Skye's Home
  latitude: 23.467909
  longitude: 120.465559
  elevation: 64
  unit_system: metric
  time_zone: Asia/Taipei

# 啟用核心功能  
default_config:  
  
# 日誌記錄  
logger:
  default: info

# 在這裡加入 MQTT 裝置
mqtt:
  sensor:                                       # sensor 裝置
    - name: "Bedroom Temperature"               # 裝置名稱
      state_topic: "home/bedroom/temperature"   # 訂閱 home/bedroom/temperature 主題
      # ...
    # ...
  #...
```

# 常見的 MQTT Device
<table>
  <tr>
    <td><a href="#">Alarm control panel</a></td>
    <td><a href="#">Binary sensor</a></td>
    <td><a href="#">Button</a></td>
    <td><a href="#">Camera</a></td>
    <td><a href="#">Climate (HVAC)</a></td>
    <td><a href="#">Cover</a></td>
    <td><a href="#">Device tracker</a></td>
    <td><a href="#">Event</a></td>
  </tr>
  <tr>  
    <td><a href="#">Fan</a></td>
    <td><a href="#">Humidifier</a></td>
    <td><a href="#">Image</a></td>
    <td><a href="#">Lawn mower</a></td>
    <td><a href="#light-device">Light</a></td>
    <td><a href="#">Lock</a></td>
    <td><a href="#">Notify</a></td>
    <td><a href="#">Number</a></td>
  </tr>
  <tr>
    <td><a href="#">Scene</a></td>
    <td><a href="#">Select</a></td>
    <td><a href="#">Sensor</a></td>
    <td><a href="#">Siren</a></td>
    <td><a href="#">Switch</a></td>
    <td><a href="#">Text</a></td>
    <td><a href="#">Update</a></td>
    <td><a href="#">Vacuum</a></td>
  </tr>
  <tr>
    <td><a href="#">Valve</a></td>
    <td><a href="#">Water heater</a></td>
  </tr>
</table>
Light（燈具）、Switch（開關）、Sensor（感測器）、Binary Sensor（二元感測器）、Button（按鈕）

# Light Device
- MQTT燈光平台能讓您透過 `default` (默認)、`json` 或 `template` (模板) 的訊息架構來控制啟用了 MQTT 功能的燈光設備。
- 以下僅介紹**使用默認 (`default`) 架構**控制啟用 MQTT 功能的燈光設備，它支援以下功能：設定亮度、色溫、效果、開/關、RGB 色彩、XY 色彩、白光。

### 基本配置
- 在理想情況下，MQTT 設備應該有一個 `state_topic`（狀態主題）來發布狀態變更。
- 如果這些訊息使用了 `RETAIN` 標誌，MQTT 燈光在訂閱後會立即收到正確的狀態更新並啟動。
  - 如果沒有使用 `RETAIN` 標誌，開關的初始狀態將未知。
- MQTT device 可以使用 `None` payload 重置當前狀態為 `unknown`。

在 `configuration.yaml` 文件中添加以下內容：
```
mqtt:
  - light:                                        # 燈光 MQTT device
      command_topic: "office/rgb1/light/switch"   # 用來控制開關的 MQTT topic
```
更改 `configuration.yaml` 後，請重啟 Home Assistant 以使用更改。

### 參數說明
- 可用性 (available) 相關參數
  | **參數** | **重要性** | **說明** |
  | ------- | ---- | -------- |
  | `availability_topic` | 可選 | (字串) 訂閱以接收設備可用性（在線/離線）狀態更新的 MQTT 主題。 |
  | `availability` | 可選 | (列表) 一組 MQTT 主題，用於訂閱設備的在線或離線 (online/offline) 狀態更新<br/>**配置了此選項，則不能同時使用 `availability_topic`** |
  | `payload_available` | 可選 | (字串) 當設備處於「可用狀態」時，接收到的 MQTT payload (默認 `online`) |
  | `payload_not_available` | 可選 | (字串) 當設備處於「不可用狀態」時，接收到的 MQTT payload (默認 `offline`) |
  | `value_template` | 可選 | (字串) 定義一個模板，模板的結果將與 `payload_available` 和 `payload_not_available` 進行比較，以確定設備的可用性。 |
  | `availability_mode` | 可選 | 將實體設置為「可用」所需的條件。<br/>`all`：只有當**所有配置的可用性主題**都收到 `payload_available` 時，設備才會被標記為在線。<br/>`any`：只要**至少有一個配置的可用性**主題收到 `payload_available`，設備就會被標記為在線。<br/>`latest`：最近一次收到的 `payload_available` 或 `payload_not_available` 控制設備的可用性。 |
  ```
  light:  
    name: "Bedroom Light"  
    command_topic: "home/bedroom/light/switch"  
    availability:                               # 一組 MQTT 主題列表，用於訂閱設備的在線或離線 (online/offline) 狀態更新
      - topic: "home/bedroom/light/state"           # 訂閱設備 在線/離線狀態 更新的 MQTT 主題。
        payload_available: "online"                 # (可選，默認 online)  當設備處於「可用狀態」時，接收到的 MQTT payload
        payload_not_available: "offline"            # (可選，默認 offline) 當設備處於「不可用狀態」時，接收到的 MQTT payload
        value_template: "{{ value_json.status }}"   # (可選) 定義一個模板，模板的結果將與 payload_available 和 payload_not_available 進行比較，以確定設備的可用性。

  # 模板 "{{ value_json.status }}" 說明
  # 如果消息是 JSON 格式，value_json 允許你直接訪問 JSON 中的鍵值對。
  # value_json.status 表示提取 JSON 消息中的 status 屬性。
  # 例如 {"status": "online", "brightness": 255 } 會提取 status 屬性的值 "online"。
  ```
  ```
  light:  
    name: "Bedroom Light"  
    command_topic: "home/bedroom/light/switch"  
    availability_topic: "home/bedroom/light/state"  # 當只有一個與可用性相關的 topic 時使用
    payload_available: "online"
    payload_not_available: "offline"
    value_template: "{{ value_json.status }}"
  ```
- 亮度 (brightness) 相關參數
  | **參數** | **重要性** | **說明** |
  | ------- | ---- | -------- |
  | `` |  |  |

### 範例
```
# 支援亮度和 RGB 燈光
mqtt:  
  - light:                                                      
      name: "Office Light RGB"  
      state_topic: "office/rgb1/light/status"  
      command_topic: "office/rgb1/light/switch"  
      brightness_state_topic: "office/rgb1/brightness/status"  
      brightness_command_topic: "office/rgb1/brightness/set"  
      rgb_state_topic: "office/rgb1/rgb/status"  
      rgb_command_topic: "office/rgb1/rgb/set"  
      state_value_template: "{{ value_json.state }}"  
      brightness_value_template: "{{ value_json.brightness }}"  
      rgb_value_template: "{{ value_json.rgb | join(',') }}"  
      qos: 0  
      payload_on: "ON"  
      payload_off: "OFF"  
      optimistic: false  
```





