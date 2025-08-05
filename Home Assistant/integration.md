Home Assistant 提供多種方式來新增裝置（integrations），以下是詳細的教學，涵蓋 Home Assistant 的 4 種新增裝置方式。

# 方式 1：透過 UI 設定介面新增裝置
- 這是最簡單、最直觀的方式，適合新手。
- 步驟：
  1. **登入 Home Assistant**
     - 使用瀏覽器打開 Home Assistant 的網頁介面（通常是 `http://<你的 Home Assistant IP>:8123`）。
     - 使用你的帳戶登入。
  2. **進入「設定」頁面**
     - 點擊左側側邊欄的「設定」（Settings）。
  3. **選擇「裝置與服務」**
     - 在設定頁面中，選擇「裝置與服務」（Devices & Services）。
  4. **新增整合（Integration）**
     - 在裝置與服務頁面，點擊右下角的「新增整合」（Add Integration）按鈕。
     - 搜尋你想要新增的裝置或服務，例如「Philips Hue」、「Google Home」、「Zigbee」等。
     - 按照提示進行設置，例如輸入 API 金鑰、登入帳戶、選擇裝置等。
  5. **完成設置**
     - 完成後，你的裝置將顯示在「裝置與服務」頁面，並可以開始使用。

# 方式 2：透過 YAML 手動配置
- 這種方式適合進階用戶，特別是當某些整合需要細節設定或不支援 UI 配置時。
- 步驟：
  1. **確保裝置安裝完成**
     - 根據裝置的使用手冊，進行初始設置。
       - 如果是 Zigbee 裝置，可能需要配對至 Zigbee Hub。
       - 如果是 MQTT 裝置，需要配置 MQTT Broker 地址。
       - 如果是 Wi-Fi 裝置，請確保其連接到正確的 Wi-Fi SSID。
     - 記錄裝置必要資訊
       - 裝置的 IP 地址（通常可以在路由器的管理介面中找到）。
       - 裝置的 API 金鑰或 Token（部分裝置需要）。
       - MQTT Broker 的地址及登入憑據（若使用 MQTT）。
  3. **找到 Home Assistant 的配置檔案**
     - Home Assistant 的核心配置檔案為 `configuration.yaml`，通常位於主機上的 Home Assistant 安裝目錄中。
  4. **編輯 `configuration.yaml`**
     - 根據整合的官方文件，新增對應的配置。
     - 以**燈泡（例如 Philips Hue）** 為例
       - `platform`：指定整合平台，這裡是 hue。
       - `host`：指定燈泡的主機 IP 地址。
       ```
       light:
       - platform: hue
         host: 192.168.1.100  
       ```
     - **MQTT 裝置**
       - 在 `configuration.yaml` 文件中新增以下配置。 
       ```
       mqtt:
         broker: 192.168.1.101
         username: myuser
         password: mypassword  
       ```
     - **Zigbee 裝置**
       - 在 `configuration.yaml` 文件中新增以下配置。
       - `usb_path`：指定 Zigbee Hub 的 USB 接口。
       - `database_path`：指定 Zigbee 裝置的資料庫存儲位置。
       ```
       zha:
         usb_path: /dev/ttyUSB0
         database_path: /config/zigbee.db  
       ```
     - 為了安全，API 金鑰、密碼等敏感資訊可以存儲在 `secrets.yaml` 文件中。
       - 在 `configuration.yaml`：
         ```
         mqtt:
           broker: !secret mqtt_broker
           username: !secret mqtt_user
           password: !secret mqtt_password  
         ```
       - 在 `secrets.yaml` 中新增：
         ```
         mqtt_broker: 192.168.1.101
         mqtt_user: myuser
         mqtt_password: mypassword  
         ```
  5. **檢查配置是否正確**
     - 編輯完成後，回到 Home Assistant 的 UI。
     - 進入「設定」 > 「檢查配置」（Check Configuration），確認 YAML 語法無誤。
  6. **重新啟動 Home Assistant**
     - 在 Home Assistant 的「設定」 > 「系統」頁面中，選擇重新啟動（Restart）。

# 方式 3：透過 HACS 安裝擴充整合
- HACS（Home Assistant Community Store）是社群開發的整合商店，提供許多非官方的整合。
- 步驟：
  1. **安裝 HACS**
     - 確保 Home Assistant 已啟用「進階模式」（Advanced Mode）。
     - 打開 Home Assistant 的網頁介面，進入「設定」 > 「附加元件」（Add-ons）。
     - 搜尋並安裝 HACS 附加元件。
  2. **設定 HACS**
     - 安裝完成後，進入 HACS，並完成授權設定（通常需要 GitHub 帳戶）。
  3. **搜尋並安裝整合**
     - 在 HACS 的介面中，搜尋你需要的整合。
     - 點擊「安裝」，並按照指示完成設置。
  4. **在 Home Assistant 中啟用整合**
     - HACS 安裝的整合通常需要手動在 `configuration.yaml` 中配置，請參考該整合的文件。

# 方式 4：自動偵測裝置
- Home Assistant 具有強大的自動偵測功能，可以自動識別網路上的裝置。
- 步驟：
  1. **確保裝置在同一網路**
     - 確保你的智慧家居裝置（例如智能燈泡、智能插座）與 Home Assistant 在同一網路中。
  2. **查看通知**
     - 如果 Home Assistant 偵測到新裝置，會在右上角顯示通知。
     - 點擊通知，然後按照指示完成設置。
  3. **手動檢查自動偵測**
     - 如果沒有收到通知，可以進入「設定」 > 「裝置與服務」，點擊「新增整合」來檢查是否有偵測到的裝置。


# 實戰範例：新增 MQTT 裝置
### 前置準備
- 確認 MQTT Broker 已運行
- 確認 MQTT 裝置已連接到 MQTT Broker
- 記錄裝置的通訊主題（Topic）
  - MQTT 使用主題（Topic）來進行裝置通訊。您需要知道每個裝置的主題。
  - 主題通常可以在裝置的文件中找到，或通過 MQTT Broker 的工具（例如 MQTT Explorer）進行監聽。

### 配置 MQTT 整合
- 首先需要在 `configuration.yaml` 中配置 MQTT 整合，以告知 Home Assistant 連接到正確的 MQTT Broker。
  ```
  mqtt:  
    broker: 192.168.1.101  
    username: myuser  
    password: mypassword  
  ```
- 重啟 Home Assistant：修改配置檔案後，進入 Home Assistant 的「設定」 > 「系統」頁面，點擊「重新啟動」（Restart）。
- 確認 MQTT 整合是否啟用：在 Home Assistant 的 UI 中，進入「設定」 > 「裝置與服務」，檢查是否已啟用 MQTT 整合。

### 新增智慧插座
- 假設智慧插座使用 MQTT 通訊，其主題如下：
  - 開關狀態 (state) 主題：`home/plug1/state`（值可能為 ON 或 OFF）
  - 控制 (command) 主題：`home/plug1/set`（值可能為 ON 或 OFF）
- 在 `configuration.yaml` 中新增以下內容：
  ```
  switch:  
  - platform: mqtt                   # 使用 MQTT 平台
    name: "智慧插座"                  # 裝置名稱，在 Home Assistant UI 中顯示
    state_topic: "home/plug1/state"  # 裝置的狀態主題，Home Assistant 會監聽此主題以了解插座的狀態
    command_topic: "home/plug1/set"  # 控制主題，Home Assistant 會向此主題發送指令來控制插座
    payload_on: "ON"                 # 指定 MQTT 消息中代表「開啟」的值
    payload_off: "OFF"               # 指定 MQTT 消息中代表「關閉」的值
    state_on: "ON"                   # 指定裝置狀態中代表「開啟」的值
    state_off: "OFF"                 # 指定裝置狀態中代表「關閉」的值
    retain: true                     # 確保 MQTT Broker 保留最新狀態。
  ```

### 新增溫濕度感測器
- 假設溫濕度感測器使用 MQTT 通訊，其主題如下：
  - 溫度主題：`home/sensor1/temperature`
  - 濕度主題：`home/sensor1/humidity`
- 在 `configuration.yaml` 中新增以下內容：
  ```
  sensor:  
  - platform: mqtt                           # 使用 MQTT 平台
    name: "溫度感測器"  
    state_topic: "home/sensor1/temperature"  # 感測器的狀態主題，Home Assistant 會監聽此主題以獲取數據
    unit_of_measurement: "°C"                # 數值的單位，例如溫度的 °C 和濕度的 %。
    value_template: "{{ value | float }}"    # 處理 MQTT 消息的模板，將消息值轉換為浮點數。
  - platform: mqtt  
    name: "濕度感測器"  
    state_topic: "home/sensor1/humidity"  
    unit_of_measurement: "%"  
    value_template: "{{ value | float }}"  
  ```

### 檢查配置 & 驗證裝置
- 檢查 YAML 語法：在 Home Assistant 的 UI 中，進入「設定」 > 「檢查配置」（Check Configuration），確認 YAML 語法是否正確。
- 重啟 Home Assistant：修改配置檔案後，重新啟動 Home Assistant。
- 查看裝置狀態
  - 在 Home Assistant 的「概覽」（Overview）頁面，檢查新增的裝置是否顯示。
  - 嘗試控制智慧插座或查看溫濕度感測器的數據。
- 使用 MQTT 工具進行監聽：如果裝置未正確運作，可以使用 MQTT Explorer 或其他 MQTT 客戶端監聽主題，確認是否有正確的消息被傳送或接收。

### (補充) MQTT Discovery 
- 開啟 MQTT Discovery：
  - Home Assistant 支援 MQTT Discovery（自動發現），可以讓裝置自動向 Home Assistant 報告其主題和配置。如果裝置支援 MQTT Discovery，可以在 configuration.yaml 中啟用。
  ```
  mqtt:  
    discovery: true  
    discovery_prefix: homeassistant
  ```  
- 檢查 MQTT Broker 日誌：如果有問題，檢查 MQTT Broker 的日誌可能有助於排查。
- 這樣，您就能完整地配置 MQTT 整合並新增 MQTT 裝置到 Home Assistant。如果有更多問題或需要進一步說明，請隨時詢問！



