# 簡介
**Home Assistant** 是一款由 Open Home Foundation 主導的開源 (Open source) 智慧家庭中樞系統，也就是所有對 Home Assistant 有熱忱的程式開發者都可以自主對 Home Assistant 進行優化開發，所以在每次的更新中都可以看到各路大神對 Home Assistant 的優化。
<br/><br/>
Home Assistant 作為一款開源智慧家庭中樞系統，可以支援幾乎市面上所有品牌的智慧家庭裝置，在介面、自動化、情境設定方面有著比一般品牌中樞系統 (Apple HomeKit、Google Home、Amazon Alexa …等) 更大的自定義靈活度，完全可以依照使用習慣自訂專屬的操作介面和自動化機制，不論是小家庭或是大莊園，Home Assistant 都可以滿足你智慧家庭懶人生活的一切需求。

# Home Assistant 硬體選擇
- Home Assistant Green
  - Home Assistant Green 堪稱最懶人的入坑姿勢，即插即用，完全不用煩惱如何安裝 Home Assistant 系統，如果你是完全沒有組電腦、安裝系統經驗的人，那 Home Assistant Green 就是你最佳的選擇。
  - Home Assistant Green 本身是封裝好的硬體，在常規的狀況下是無法進行有效的硬體擴充 (儲存容量、記憶體…等)
- 樹莓派 (Raspberry Pi)
  - 樹莓派因為本身的體積小、低功耗 (低耗電) 的特性，特別適合拿來運行 Home Assistant 這類對硬體需求不高的作業系統 (Operation System, OS)。
  - 而樹莓派也是除了 Home Assistant Green 及 Home Assistant Yellow 這兩個自家硬體之外官方首推的 Home Assistant 建置方案。
- Home Assistant Yellow
  - Home Assistant Yellow 是官方在 2021 年推出的產品，是專為 Home Assistant 系統設計的硬體設備。
  - **內建 Zigbee、Thread 無線模組**：內置 Silicon Labs EFR32 無線模組支援 Zigbee, Thread, Matter 連線，幾乎所有智慧家庭裝置都適用。
  - 需要自行安裝 CM4/CM5
- Intel NUC
- NAS

# Home Assistant 安裝方式選擇
Home Assistant的本體，其實是架在系統 (通常為Linux) 內的Python程式，稱做 `Home Assistant Core`，通常以Docker Container來單獨安裝。
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
  - Home Assistant 支援所有智慧家庭連線方式：Wi-Fi、Zigbee、Bluetooth、Thread、Matter、zWave，可以整合市面上幾乎所有的智慧家庭裝置。

# 使用 Docker 安裝 Home Assistant
```
docker run --init -d \ 
  --name homeassistant \  
  --restart=unless-stopped \  
  -p 8123:8123 \  
  -v /path/to/your/config:/config \  
  ghcr.io/home-assistant/home-assistant:stable  
```
使用瀏覽器訪問 `http://<你的伺服器 IP>:8123` 完成初始設定。

# 初始設定
- **建立使用者帳戶**：
  - 設置你的帳號名稱、密碼，並選擇你的家庭位置和時區。
- **連接設備**：
  - Home Assistant 支援大量的設備和服務，透過「整合（Integrations）」功能添加設備。
  - 在主頁面點擊「設定 > 整合」，搜尋你想添加的設備（例如 Philips Hue、Google Home）。
- **設置自動化**：
  - 點擊「設定 > 自動化」，建立自動化流程。
  - 例如：「當太陽下山時，開啟客廳燈光」。

# 使用 Home Assistant
- **控制面板**
  - Home Assistant 的主介面是「Lovelace UI」，你可以根據需求自定義面板。
  - 在主頁面上點擊右上角的「編輯儀表板」，添加卡片（例如燈光控制、溫度顯示）。
- **添加更多整合**
  - 前往「設定 > 整合」，探索各種支援的設備和服務。
  - 例如，連接 Spotify、Alexa、Google Assistant。
- **YAML 配置文件**
  - Home Assistant 的核心是 **YAML 文件**，所有的設定都可以透過 YAML 自定義。
  - 配置文件位於 `/config` 資料夾，主要包括：
    - `configuration.yaml`：主設定檔。
    - `automations.yaml`：自動化設定。
    - `scripts.yaml`：腳本設定。

# 進階功能
- 使用 Node-RED
  - Node-RED 是一個圖形化的編排工具，可以用來設計複雜的自動化流程。
  - 你可以透過 Home Assistant 添加 Node-RED 整合。
- 安裝 HACS（Home Assistant Community Store）
  - HACS 是一個社群插件商店，提供大量的自訂卡片和整合。
  - 安裝方法：在主頁面點擊「設定 > 附加元件」，搜尋 HACS，並啟用它。
- 設置安全性
  - 開啟 HTTPS（SSL）以保護你的 Home Assistant。
  - 使用 DuckDNS 或 NGINX Proxy Manager 設置域名和 SSL。

# 參考資料
- 官方文件：Home [Assistant Documentation](https://www.home-assistant.io/docs/)
- 社群論壇：Home [Assistant Forum](https://community.home-assistant.io/)
