# 專案結構
- 建立 mqtt 使用者帳密檔 (`passwordfile`)：
  ```
  docker container run --rm -it -v ./mqtt:/mosquitto \
    eclipse-mosquitto mosquitto_passwd -c /mosquitto/passwordfile skye
  ```
- 資料夾結構
  ```
  - mqtt_ha
    - mqtt
      - config
        - mosquitto.conf
      - data
      - log
      - passwordfile
    - HA_config
      - configuration.yaml
  ```
  
# MQTT 配置檔
### mosquitto.conf
```
persistence true
persistence_location ../data/
log_dest file ../log/mosquitto.log

allow_anonymous false
password_file /mosquitto/passwordfile

listener 1883
protocol mqtt
```

# Home Assistant 配置檔
### configuration.yaml
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

# 加入 MQTT 裝置
mqtt:
  # 裝置 1：訂閱 home/bedroom/temperature 主題
  sensor:
    - name: "Bedroom Temperature"
      state_topic: "home/bedroom/temperature"
```

# Home Assistant 設定
### 加入 MQTT Broker
- 設定路徑：
  ```
  左側側邊欄 --> 設定（Settings）--> 裝置與服務（Devices & Services）--> 右下角的「新增整合」（Add Integration）--> 輸入「MQTT」
  ```
- 設定以下欄位： 
  - Broker: `localhost`
  - 通訊埠: `1883`
  - 使用者名稱、密碼
