# 教學目標
- 初始配置 Home Assistant，包含基本設定。
- 整合智慧裝置與服務。
- 設定自動化與通知。
- 優化與備份系統。

# 第一部分：初始配置
### 初次啟動與登入
- 啟動 Docker 容器後，訪問 `http://<主機IP>:8123`。
- 第一次登錄時，需要設定：**帳號**（管理員帳號）、**位置**（用於天氣、日出日落等功能）、**時區**（例如 Asia/Taipei）、**是否加入匿名統計**（可選）。

### 配置文件結構
- Home Assistant 的主要配置文件存放在 `/config` 目錄，核心文件包括：
  - `configuration.yaml`：主配置文件，幾乎所有設定都集中在此。
  - `automations.yaml`：儲存自動化規則。
  - `scripts.yaml`：儲存自訂腳本。
  - `secrets.yaml`：儲存敏感資訊（如 API 金鑰、密碼）。

### 配置基本資訊
修改主配置文件 `configuration.yaml`，添加以下內容：
```
homeassistant:  
  name: My Smart Home  
  atitude: 25.0330  
  longitude: 121.5654  
  elevation: 10  
  unit_system: metric  
  time_zone: Asia/Taipei  
  external_url: "http://<主機IP>:8123"  
  
# 啟用核心功能  
default_config:  
  
# 日誌記錄  
logger:  
  default: info  
```
- `atitude` 和 `longitude` 是您的地理位置座標，而 `elevation` 是海拔高度（公尺，影響天氣預測）。
- `unit_system` 設為 `metric` 使用公制（如 °C、公里）。
- `default_config` 啟用預設功能，包括：設備自動發現、日誌記錄、界面設置等。
  - 如果希望禁用某些功能（例如自動發現），可以移除 default_config 並手動啟用所需功能。

完成後，重啟容器：
```
docker restart homeassistant
```

# 第二部分：整合智慧裝置與服務
### 使用介面新增整合
- 前往 Settings > Devices & Services > Add Integration。
- 搜尋您要整合的服務或裝置，例如：
  - **MQTT**：用於智慧家居通訊協議。
  - **Google Home**：整合 Google Assistant。
  - **Zigbee**：使用 Zigbee 硬體（如 Zigbee2MQTT 或 ZHA）。
- MQTT整合
  - 修改 `configuration.yaml`：
    - broker 是 MQTT 伺服器的 IP 地址。
    - username 和 password 是 MQTT 的認證資訊。
  ```
  mqtt:  
    broker: <MQTT Broker IP>  
    username: <MQTT Username>  
    password: <MQTT Password>
  ```
- Zigbee 整合
  - Zigbee 裝置需要使用 Zigbee USB Dongle（例如 Conbee II），並選擇以下整合之一：
  - ZHA (Zigbee Home Automation)：
    - usb_path：USB Dongle 的設備路徑。
    - database_path：Zigbee 數據庫保存位置。
    ```
    zha:  
      usb_path: /dev/ttyUSB0  
      database_path: /config/zigbee.db
    ```
  - Zigbee2MQTT：
    - 配置 Zigbee2MQTT 與 MQTT 整合。
    ```
    mqtt:  
      broker: 192.168.1.100  
      username: mqtt_user  
      password: mqtt_password

    zigbee2mqtt:  
      base_topic: zigbee2mqtt
    ```
- Google Home 或 Alexa
  - 若要整合 Google Home 或 Alexa，需要啟用 cloud。
    - include_entities：允許 Google Home 控制的設備。
    - exclude_entities：排除 Google Home 控制的設備。
  ```
  cloud:  
    google_actions:  
      filter:  
        include_entities:  
          - light.living_room  
        exclude_entities:  
          - switch.kitchen
  ```
- 智慧燈具（例如 Philips Hue）
  - 如果您有 Philips Hue 燈具，需要安裝官方整合。
    - host：Philips Hue Hub 的 IP 地址。 
  ```
  light:  
    - platform: hue  
      host: 192.168.1.200  
  ```

### 設定 Zigbee/Z-Wave
- 如果您有 Zigbee 或 Z-Wave 裝置，則需要相關硬體（如 USB Dongle）以及整合。
- Zigbee：
  - 安裝 Zigbee2MQTT 或 ZHA。
  - 修改 `configuration.yaml` 以啟用 Zigbee。
- Z-Wave：
  - 使用 Z-Wave JS 整合。

### 設定其他常見設備
- **智慧燈具**（如 Philips Hue）：新增整合並連接到燈具的 Hub。
- **智慧插座**：確保插座支援的協議（如 Zigbee、Wi-Fi）。
- **監控攝影機**：使用 ONVIF 或相關整合。

# 第三部分：設定自動化與通知
### 使用介面設定自動化
- 前往 Settings > Automations & Scenes > Create Automation。
- 選擇觸發條件（Trigger），例如：
  - 時間觸發（日落時）。
  - 裝置狀態觸發（燈具開啟）。
  - 設定動作（Action），例如：
  - 開燈。
  - 傳送通知。

### 手動編寫 YAML 自動化
修改 `automations.yaml`。
- `trigger`：觸發條件（例如日落）。
- `action`：執行的動作（例如開燈）。
```
automation:  
  - alias: Turn on lights at sunset  
    trigger:  
      - platform: sun  
        event: sunset  
    action:  
      - service: light.turn_on  
        target:  
          entity_id: light.living_room
```
完成後，重啟 Home Assistant：
```
docker restart homeassistant
```

### 3. 設定通知
- Home Assistant 支援多種通知方式，例如：
  - 手機通知（推送）：
    - 安裝 Home Assistant Companion App（iOS 或 Android）。
    - 在 configuration.yaml 中啟用通知：
      ```
      notify:
        name: mobile_app
        platform: mobile_app
      ```
  - 電子郵件通知：
    - 使用 SMTP 整合。
    ```
    notify:  
      - name: email_notify  
        platform: smtp  
        server: smtp.gmail.com  
        port: 587  
        encryption: starttls  
        username: your_email@gmail.com  
        password: your_password  
        sender: your_email@gmail.com  
    ```


# 第四部分：優化與備份系統
### 優化性能
- 在 `configuration.yaml` 啟用日誌記錄。
  - 將日誌層級設為 `warning` 或更高，減少無用的日誌。
  ```
  logger:  
    default: warning
  ```
- 停用未使用的整合。

### 設定備份
- 定期備份 `/config` 目錄，您可以使用以下方法：
  - 手動備份：`tar -czvf homeassistant_backup.tar.gz /path/to/config`
  - 使用版本控制：將配置文件存入 Git 儲存庫。

### 使用 HACS（Home Assistant Community Store）
- HACS 是一個社群插件市場，提供許多自訂卡片和整合。
- 安裝 HACS：
  - 前往 HACS 官方網站，按照指示安裝。
  - 使用 HACS 安裝自訂 Lovelace 卡片或整合。
    - 前往 Settings > Dashboards。
    - 使用 YAML 或介面自訂儀表板。
    - 安裝自訂卡片（例如 Mini Graph Card）：使用 HACS 安裝，將卡片添加到儀表板。

# 範例完整配置文件
```
homeassistant:  
  name: My Smart Home  
  latitude: 25.0330  
  longitude: 121.5654  
  elevation: 10  
  unit_system: metric  
  time_zone: Asia/Taipei  
  
default_config:  
  
mqtt:  
  broker: 192.168.1.100  
  username: mqtt_user  
  password: mqtt_password  
  
automation:  
  - alias: Turn on lights at sunset  
    trigger:  
      - platform: sun  
        event: sunset  
    action:  
      - service: light.turn_on  
        target:  
          entity_id: light.living_room  
  
notify:  
  - name: mobile_app  
    platform: mobile_app  
```
