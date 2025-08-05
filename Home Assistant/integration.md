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
  1. **找到 Home Assistant 的配置檔案**
     - Home Assistant 的核心配置檔案為 `configuration.yaml`，通常位於主機上的 Home Assistant 安裝目錄中。
  2. **編輯 `configuration.yaml`**
     - 使用文字編輯器（例如 VS Code 或 Notepad++）開啟 `configuration.yaml` 檔案。
     - 根據整合的官方文件，新增對應的配置。例如：
       ```
       light:
       - platform: hue
         host: 192.168.1.100  
       ```
       或：
       ```
       mqtt:
         broker: 192.168.1.101
         username: myuser
         password: mypassword  
       ```
  3. **檢查配置是否正確**
     - 編輯完成後，回到 Home Assistant 的 UI。
     - 進入「設定」 > 「檢查配置」（Check Configuration），確認 YAML 語法無誤。
  4. **重新啟動 Home Assistant**
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
