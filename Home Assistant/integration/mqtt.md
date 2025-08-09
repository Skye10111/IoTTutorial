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
