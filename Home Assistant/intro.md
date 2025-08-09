# 簡介
**Home Assistant** 是一款由 Open Home Foundation 主導的開源 (Open source) 智慧家庭中樞系統，也就是所有對 Home Assistant 有熱忱的程式開發者都可以自主對 Home Assistant 進行優化開發，所以在每次的更新中都可以看到各路大神對 Home Assistant 的優化。
<br/><br/>
Home Assistant 作為一款開源智慧家庭中樞系統，可以支援幾乎市面上所有品牌的智慧家庭裝置，在介面、自動化、情境設定方面有著比一般品牌中樞系統 (Apple HomeKit、Google Home、Amazon Alexa …等) 更大的自定義靈活度，完全可以依照使用習慣自訂專屬的操作介面和自動化機制，不論是小家庭或是大莊園，Home Assistant 都可以滿足你智慧家庭懶人生活的一切需求。

# Home Assistant 硬體選擇
- **Home Assistant Green**
  - Home Assistant Green 堪稱最懶人的入坑姿勢，即插即用，完全不用煩惱如何安裝 Home Assistant 系統，如果你是完全沒有組電腦、安裝系統經驗的人，那 Home Assistant Green 就是你最佳的選擇。
  - Home Assistant Green 本身是封裝好的硬體，在常規的狀況下是無法進行有效的硬體擴充 (儲存容量、記憶體…等)
- **樹莓派 (Raspberry Pi)**
  - 樹莓派因為本身的體積小、低功耗 (低耗電) 的特性，特別適合拿來運行 Home Assistant 這類對硬體需求不高的作業系統 (Operation System, OS)。
  - 而樹莓派也是除了 Home Assistant Green 及 Home Assistant Yellow 這兩個自家硬體之外官方首推的 Home Assistant 建置方案。
- **Home Assistant Yellow**
  - Home Assistant Yellow 是官方在 2021 年推出的產品，是專為 Home Assistant 系統設計的硬體設備。
  - 內建 Zigbee、Thread 無線模組：內置 Silicon Labs EFR32 無線模組支援 Zigbee, Thread, Matter 連線，幾乎所有智慧家庭裝置都適用。
  - 需要自行安裝 CM4/CM5
- **Intel NUC**
- **NAS**

# Home Assistant 安裝方式選擇
Home Assistant的本體，其實是架在系統 (通常為 Linux) 內的Python程式，稱做 `Home Assistant Core`，通常以Docker Container來單獨安裝。
<br/><br/>
我們一般講的 Home Assistant 其實是由 `Home Assistant Core` 以及 `Home Assistant Supervisor`，前者是 Home Assistant 本身，而後者可以協助安裝擴充元件以及維護整個 Docker 環境。
<br/><br/>
官方其實推出了一個包含 Docker 以及預設設定好的完整 Linux 系統，稱作 `Home Assistant OS`，這是一個包含 Home Assistant Core 和 Supervisor 的完整系統，我們之後的安裝方式將主要以此為主。
- **Home Assistant Operation System (HA OS)**：可以享有最完整的 Home Assistant 系統功能。
- **Home Assistant Container**：用容器 (Container) 的方式安裝 Home Assistant，通常是透過 Docker 安裝。
- **Home Assistant Core**：用來安裝在 Python 虛擬環境。
- **Home Assistant Supervised**：手動安裝 Supervisor。

# 準備工作
在開始之前，請確保你具備以下條件：
- 硬體需求：
  - 一台 Raspberry Pi（推薦使用 Raspberry Pi 4 或更高版本）。
  - 或者一台電腦/伺服器（可選擇 Docker 安裝）。
  - SD 卡（至少 32GB，建議使用高品質的卡）。
  - 家庭網路環境（Wi-Fi 或有線）。
- 軟體需求：
  - Home Assistant 的映像檔（建議使用 Home Assistant OS）。
  - Etcher（用於燒錄 SD 卡）。
- 智能設備：
  - 支援 Home Assistant 的設備，例如燈泡、插座、溫控器、攝像頭等。
  - Home Assistant 支援所有智慧家庭連線方式，如 Wi-Fi、Zigbee、Bluetooth、Thread、Matter、zWave 等等幾乎所有的智慧裝置。

# Home Assistant 基本功能
- **控制面板**
  - 提供一個可視化的界面，讓使用者方便地監控和控制智慧設備，可顯示設備狀態、感測器數據、相機影像等。 
  - Home Assistant 的主介面是「Lovelace UI」，你可以根據需求自定義面板。
  - 在主頁面上點擊右上角的「編輯儀表板」，添加卡片（例如燈光控制、溫度顯示）。
- **整合 (integration)**
  - 支援數千種設備和服務的整合，包括燈光、感測器、相機、智慧助理（Google Assistant、Alexa）、能源管理等。
  - 整合的設備可以透過 Home Assistant 進行統一管理和控制。 
  - 前往「設定 > 整合」，探索各種支援的設備和服務，例如，連接 Spotify、Alexa、Google Assistant。
- **自動化 (automation)**：
  - 點擊「設定 > 自動化」，建立自動化流程。
  - 根據觸發條件（如時間、感測器狀態改變、按鈕按下等）自動執行動作，例如：「當太陽下山時，開啟客廳燈光」。
  - 適合用於事件驅動的操作，例如感測到有人進入房間時自動開燈。
- **腳本 (Scripts)**
  - 定義多步驟操作流程，腳本可以被手動執行或由其他自動化觸發。
  - 支援延遲、條件判斷等功能，用於簡化重複使用的複雜操作。
- **場景 (Scenes)**
  - 將多個設備狀態儲存為一個場景，方便一次性切換，例如 "電影模式" 可以設定燈光暗化、電視開啟、音響啟動等。
  - 場景可以透過控制面板或自動化觸發。
- **設備追蹤 (Device Tracking)**
  - 追蹤使用者或設備的地理位置（例如透過 GPS、Wi-Fi、藍牙），支援多種位置追蹤整合（如 iOS App、Life360）。
  - 可用於基於位置的自動化，例如當使用者回家時開燈。
- **通知 (Notifications)**
  - 支援多種通知方式，包括手機推送 (如 iOS/Android)、電子郵件、語音提醒等。
  - 可用於自動化規則的通知，例如感測器偵測到門窗打開時發送警報。
- **能源管理 (Energy Management)**
  - 監控家庭能源使用情況（如電力、天然氣、水）。
  - 支援整合智慧電錶和能源感測器。
- **開放 API (Open API)**
  - 提供 REST API 和 WebSocket API，方便進行程式化控制或與其他系統整合。
  - 適合開發者進行自訂擴展。
- **語音控制 (Voice Control)**
  - 支援整合智慧助理（如 Google Assistant、Amazon Alexa）進行語音控制。
  - 可用語音開燈、調整溫度、啟動場景等。
- **藍圖 (Blueprints)**
  - 提供可重複使用的自動化模板，讓使用者快速建立自動化規則。
  - 輕鬆分享和匯入其他使用者的藍圖設計。
- **擴展功能 (Add-ons & Custom Components)**
  - 支援安裝附加元件（如 File Editor、Node-RED、MQTT Broker）來擴充功能。
  - 開發者可以使用自訂元件來增加新功能。
  - **HACS (Home Assistant Community Store)** 是一個社群插件市場，提供許多自訂卡片和整合。

# 配置方式
- Home Assistant 的核心是 **YAML 文件**，所有的設定都可以透過 YAML 自定義。
- Home Assistant 的主要配置文件存放在 `/config` 目錄，核心文件包括：
  - `configuration.yaml`：主配置文件，幾乎所有設定都集中在此。
  - `automations.yaml`：儲存自動化規則。
  - `scripts.yaml`：儲存自訂腳本。
  - `secrets.yaml`：儲存敏感資訊（如 API 金鑰、密碼）。

# 參考資料
- 官方文檔：https://www.home-assistant.io/docs/
- 社群論壇：https://community.home-assistant.io/
- 中文網 (非官方)：https://home-assistant-china.github.io/
