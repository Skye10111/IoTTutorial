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


# 加入 MQTT 整合
```
1. 設定 --> 裝置與服務 --> 在右下角點選「新增整合」按鈕

2. 從列表中選擇 MQTT。

3. 按照螢幕上的指示完成設定。
  - Broker   : MQTT broker 的主機名稱或 IP 位址。
  - 通訊埠    : MQTT broker 所監聽的通訊埠 (預設 1883)
  - 使用者名稱 : 登入 MQTT broker 的使用者名稱。
  - 密碼      : 登入 MQTT broker 的密碼。
```

# 加入 MQTT Broker
- 設定路徑：
  ```
  左側側邊欄 --> 設定（Settings）--> 裝置與服務（Devices & Services）--> 右下角的「新增整合」（Add Integration）--> 輸入「MQTT」
  ```
- 設定以下欄位： 
  - Broker: `localhost`
  - 通訊埠: `1883`
  - 使用者名稱、密碼

# 加入 MQTT 裝置
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
```
