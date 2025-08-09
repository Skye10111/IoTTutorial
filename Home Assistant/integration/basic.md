# 主要配置方式
- Home Assistant 提供多種方式來新增裝置（integrations），以下是 4 種新增裝置方式。
  - 透過 UI 設定介面新增裝置
  - 透過 YAML 手動配置
  - 透過 HACS 安裝擴充整合
  - 自動偵測裝置

# 透過 UI 設定介面新增裝置
1. **登入 Home Assistant**
2. **進入「設定」頁面，選擇「裝置與服務」**
3. **點擊右下角的「新增整合（Integration）」按鈕**
   - 搜尋你想要新增的裝置或服務，例如「Philips Hue」、「Google Home」、「Zigbee」等。
   - 按照提示進行設置，例如輸入 API 金鑰、登入帳戶、選擇裝置等。
4. **完成後，你的裝置將顯示在「裝置與服務」頁面，並可以開始使用。**

# 透過 YAML 手動配置
1. **確保裝置安裝完成**
   - 如果是 Zigbee 裝置，可能需要配對至 Zigbee Hub。
   - 如果是 MQTT 裝置，需要配置 MQTT Broker 地址。
   - 如果是 Wi-Fi 裝置，請確保其連接到正確的 Wi-Fi SSID。
2. **編輯 `configuration.yaml`**
   - 根據整合的官方文件，新增對應的配置。
   - **MQTT 裝置**
     - 在 `configuration.yaml` 文件中新增以下配置。 
     ```
     mqtt:
       # 加入 MQTT 裝置
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
3. **檢查配置是否正確**
   - 編輯完成後，回到 Home Assistant 的 UI。
   - 進入「設定」 > 「檢查配置」（Check Configuration），確認 YAML 語法無誤。
4. **重新啟動 Home Assistant**
   - 在 Home Assistant 的「設定」 > 「系統」頁面中，選擇重新啟動（Restart）。

# 方式 3：透過 HACS 安裝擴充整合
HACS（Home Assistant Community Store）是社群開發的整合商店，提供許多非官方的整合。
1. **安裝 HACS**
   - 確保 Home Assistant 已啟用「進階模式」（Advanced Mode）。
   - 打開 Home Assistant 的網頁介面，進入「設定」 > 「附加元件」（Add-ons），搜尋並安裝 HACS 附加元件。
2. **安裝完成後，進入 HACS，並完成授權設定（通常需要 GitHub 帳戶）。**
3. **搜尋並安裝整合**
   - 在 HACS 的介面中，搜尋你需要的整合。
   - 點擊「安裝」，並按照指示完成設置。
4. **在 Home Assistant 中啟用整合**
   - HACS 安裝的整合通常需要手動在 `configuration.yaml` 中配置，請參考該整合的文件。

# 方式 4：自動偵測裝置
Home Assistant 具有強大的自動偵測功能，可以自動識別網路上的裝置。
1. **確保裝置與 Home Assistant在同一網路**
2. **查看通知**
   - 如果 Home Assistant 偵測到新裝置，會在右上角顯示通知。
   - 點擊通知，然後按照指示完成設置。
3. **手動檢查自動偵測**
   - 如果沒有收到通知，可以進入「設定」 > 「裝置與服務」，點擊「新增整合」來檢查是否有偵測到的裝置。

# MQTT Discovery (補充)  
- Home Assistant 支援 MQTT Discovery（自動發現），可以讓裝置自動向 Home Assistant 報告其主題和配置。
- 如果裝置支援 MQTT Discovery，可以在 `configuration.yaml` 中啟用。
  ```
  mqtt:  
    discovery: true  
    discovery_prefix: homeassistant
  ```  
- 檢查 MQTT Broker 日誌：如果有問題，檢查 MQTT Broker 的日誌可能有助於排查。
