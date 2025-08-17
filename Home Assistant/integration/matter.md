# 簡介
見 [Matter 說明](/IOT/matter.md)筆記。

### 推薦安裝：Home Assistant OS
- Home Assistant OS 是目前 HA 唯一正式支援運行 Matter 附加元件 (add-on) 的安裝類型。
- 此安裝方式提供官方支持，確保 Matter 整合的穩定性和兼容性。
- 如果您以其他方式運行 Home Assistant（例如在容器中運行），Matter 附加元件的支持可能有限，並且需要您自行解決可能出現的問題。
  - 在容器化環境中運行 Matter Server 可能需要額外的配置，例如網絡設置和端口映射。
  - 使用容器時，Matter 整合可能不如 Home Assistant OS 中的附加元件穩定。

# 將 Matter 設備添加到 Home Assistant
- 在 Matter 技術中，每個 Matter 網絡被稱為「Fabric」，每個控制 Matter 設備的家庭自動化控制器都有自己的 Fabric。
  - 您可以**直接將設備添加到 Home Assistant 的 Fabric 中**。
  - 或者，**從其他 Fabric（例如 Google 或 Apple 的 Fabric）共享設備到 Home Assistant 的 Fabric**。
- Home Assistant 的 Matter 控制器與 Matter 設備之間的通信是加密的，Thread Border Router 僅負責轉發數據，無法讀取其內容。

### 前置準備
- 在 Home Assistant 中，安裝 Matter 整合：
  ```
  1. 設定 --> 裝置與服務 --> 添加 Matter 整合。
     - 在提示選擇連接方法時：
       - 如果使用 Home Assistant OS 的常規設置：
         - 選擇「提交」（Submit），這將安裝官方 Matter Server 附加元件 (Add on)。
         - 注意：官方Matter Server附加元件不支持32位平台。
       - 如果您已在其他附加元件或自定容器中運行 Matter Server：
         - 取消選中該選框，然後選擇「提交」（Submit）。
         - 在下一步中，提供您 Matter Server 的 URL。

  2. 檢查設備包裝上的標誌
     - 在設備包裝上檢查是否有 Matter 標誌以及 Wi-Fi 或 Thread 標誌。
     - 確認設備是否包含 QR 碼或數字設置碼，這些信息在設備加入過程中必不可少。

  3. 準備 iPhone 以添加 Matter 設備
     - 確保已安裝最新版本的 Home Assistant Companion App。
     - 確保您的手機與 Matter 設備 (還有 Border Router) 處於近距離範圍內。
     - 如果您使用的是 Thread 網絡：
       - 確保您的家庭網絡中有一個 Thread Border Router，例如：
         - HomePod Mini（或第2代）。
         - Apple TV 4K。
         - Home Assistant（使用 OpenThread Border Router 附加元件）。
     - Wi-Fi 網絡要求（針對基於 Wi-Fi 的 Matter 設備）
       - 確保 Matter 設備使用的是 2.4 GHz 頻段的 Wi-Fi。
       - 許多 Matter 設備為了節省功耗，通常使用 2.4 GHz 頻率。
       - 確保您的手機也連接到同一個 2.4 GHz 的 Wi-Fi 網絡，以保證設備正常運行。
  ```
如果使用 OpenThread（例如 Connect ZBT-1 或 SkyConnect）作為 Border Router，需按照 [Thread 文檔](https://www.home-assistant.io/integrations/thread#turning-home-assistant-into-a-thread-border-router) 中的步驟完成設置。

### 使用 iOS Companion App 添加新設備
```
1. 手機打開 Home Assistant Companion App。

2. 設定 --> 裝置與服務 --> 點擊 裝置 標籤頁。

3. 點擊 添加設備 按鈕 --> 選擇 添加 Matter  設備。
   - 在對話框中，選擇 否，這是新設備（No, it’s new）。

4. 掃描 QR 碼或手動輸入設置碼
   - 使用手機相機掃描 Matter 設備上的 QR 碼。
   - 如果無法掃描 QR 碼，可以點擊 更多選項…（More options…），手動輸入設備的設置碼（Commission Code）。

5. 選擇 添加到 Home Assistant（Add to Home Assistant），以開始 Matter 設備的加入（Commissioning）過程。
   - 該過程可能需要幾分鐘。

6. 如果有提示，輸入自定義的配件名稱（Accessory Name）。
   - 您可以輸入任意名稱，該名稱僅作為 iOS 的內部參考。
   - 注意：此名稱不會顯示在 Home Assistant 中。

7. 完成設置
   - 輸入名稱後，點擊 繼續（Continue）。
   - 一旦加入過程完成，點擊 完成（Done）。

8. 使用設備
   - 您將被重定向到 Home Assistant 中的設備頁面。
   - Matter 設備現已添加成功並準備使用。
```

### 查看 Matter 設備的資訊
Matter 設備資訊部分提供了設備的一些診斷信息，幫助您了解設備的詳細配置和網絡狀態。
```
1. 設定 --> 裝置與服務 --> Matter 整合卡片 --> 選擇「設備（Devices）」

2. 查看特定設備資訊
   - 從設備列表中找到您想查看詳細信息的設備，點擊該設備以進入詳細頁面。
   - 在設備詳細頁面中，找到 Matter 設備資訊（Matter device information） 部分。
```
| 重要名詞 | 說明 |
| --- | ---- |
| 網絡類型<br/>(Network type) | 顯示設備所屬的網絡類型，例如：<br/>**Thread**：基於低功耗網狀網絡的通信技術，通常用於智能家居設備。<br/>**Wi-Fi**：基於 Wi-Fi 網絡進行通信，適用於高流量需求的設備。 |
| 設備類型<br/>(Device type) | Matter 設備類型分為以下 4 種：<br/>**終端設備（End device）**：始終保持其無線電開啟，通常不是電池供電的設備 (如：插座)。<br/>**休眠終端設備（Sleepy end device）**：不具路由功能，常處休眠狀態的設備，偶爾醒來動作 (如：感測器)。<br/>**路由終端設備（Routing end device）**：始終保持無線電開啟，並能為其他設備進行流量路由。<br/>**網橋（Bridge）**：將非 Matter 設備或子網絡整合到 Matter 網絡中 (如： Aqara Hub)。 |
| 網絡名稱<br/>(Network name) | 顯示設備在配對（commissioning）時加入的網絡名稱。 |
| IP 地址（IP addresses) | 通常顯示多個 IPv6 地址 (如果設備支持 IPv4，這裡也會顯示 IPv4 地址)。 |

# 在 HA 中共享來自其他平台的設備
如果您已將 Matter 設備添加到 Apple Home 或 Google Home，並希望能夠同時從 Apple Home 或 Google Home 以及 Home Assistant 控制它，請使用以下方法。
### 先決條件
- 安裝最新版本的 Home Assistant Companion App。
- 確保 Matter 設備已分別集成到 Apple Home 或 Google Home。
- 在 Home Assistant 中安裝 Matter 整合。
  - 設定 --> 設備與服務 --> 添加 Matter 集成
  - 當系統提示選擇「連接方式」時：
    - Home Assistant OS：選擇「提交（Submit）」，這將安裝官方的 Matter 伺服器附加組件。
    - 在自定容器中運行 Matter 伺服器（不推薦）：取消勾選該框，然後選擇「提交」，並在下一步提供 Matter 伺服器的 URL。

### 從其他 Matter 控制器共享設備（例如 Apple 或 Google Home）
若要允許 Home Assistant 控制已添加到其他 Matter 控制器（如 Google Home）的設備，請按照以下步驟操作。
```
1. 打開手機上的 Home Assistant Companion App。

2. 設定 --> 裝置與服務 --> 在「設備」標籤下，選擇「添加設備」按鈕 --> 選擇「添加 Matter 設備」。

3. 在對話框中，選擇「是，它已在使用中」，然後選擇設備已連接的控制器（例如 Google Home）。

4. 按照對話框中提供的指示進行操作。

5. 一旦設備成功添加到 Home Assistant，您會看到通知「您的設備已添加」。
   - 完成過程後，系統會將您重定向到 Home Assistant 中的設備頁面。
   - 現在，您可以從 Home Assistant 和 Google Home 控制您的設備。
```

# 使用 Matter Bridge
- 在某些生態系統中，您可以**通過 Matter bridge 將其非 Matter 設備添加到 Home Assistant**。
- 例如，以下是一些常見的 Matter 網橋：
  - SwitchBot Hub 2
  - Aqara Hub M2、M3
  - Ikea Dirigera
  - Philips Hue Bridge
- 使用 Matter 網橋的好處是，您可以**繼續通過這些設備的原生 App 控制它們，同時也能在 HA 中使用和控制它們**。
- 什麼情況下不適合使用 Matter 網橋
  | 原因 | 說明 |
  | --- | ---- |
  | **Matter 支持的功能有限** | Matter 協議目前僅支持有限的功能集，可能不足以滿足某些高級控制需求。 |
  | **設備已支持 Home Assistant 原生集成** | HA 提供的原生整合功能更全面，用 Matter 反而受限。 |
  | **網橋性能或穩定性不足**| 某些 Matter 網橋可能存在性能瓶頸或穩定性問題，影響整體使用體驗。 |

### 將 Matter 網橋添加到 Home Assistant
```
1. 檢查設備文檔：
   - 查閱您網橋 (bridge) 設備的官方文檔或指南，了解如何啟用 Matter 配對模式及相關步驟。

2. 啟用 Matter 配對模式 (Matter-paring mode)：
   - 打開網橋設備的原生 App（例如 SwitchBot 或 Aqara App）。
   - 在 App 中啟用 Matter 配對模式。
   - 啟用後，App 通常會提供配對碼，並指示您將設備設置為配對模式。

3. 在 Home Assistant 中添加設備：
   - 打開 Home Assistant Companion App。
   - 設定 --> 裝置與服務 --> 在「設備」標籤下，選擇「添加設備」按鈕 --> 選擇「添加 Matter 設備」
   - 按照 Home Assistant 中的提示輸入配對碼並完成設置。
```

# 共享 Matter 設備到其他平台
如果您已將 Matter 設備添加到 Home Assistant，並希望將該設備共享到其他平台（例如 Google Home 或 Apple Home），您可以按照以下步驟進行操作。
```
1. 設定 --> 裝置與服務 --> Matter 設定頁面 --> 選擇「設備（Devices）」

2. 在設備列表中，找到您希望共享的裝置，點擊該裝置，進入詳細頁面。

3. 在設備頁面中，選擇「共享裝置（Share device）」。
   - 在彈出的對話框中，再次選擇「共享設備（Share device）」以確認。
   - 提示：
     - 不需要按下設備的硬體按鈕來進入配對模式，這是 Matter 設備共享的一大優勢。

4. 在其他平台的 App 中完成配對：
   - 打開您希望共享至的平台（例如 Google Home 或 Apple Home）的 App。
   - 掃描 Home Assistant 提供的 QR 碼，或手動輸入共享碼。
   - 按照對方平台 App 中的指示完成設備添加。

5. 配對完成後，回到 Home Assistant，確認設備已成功共享
   - 在「共享設備（Share device）」按鈕旁邊，點擊「⋮」菜單，選擇「管理網絡（Manage fabrics）」。
   - 在列表中，應該能看到新平台的名稱。
     - 例如，如果您共享至 Google Home，列表中應顯示「Google LLC」。
```
![image](https://www.home-assistant.io/images/integrations/matter/matter_share-device-with-other-platform.png)

# 從 Matter 控制器中移除設備
- 如果您希望將某個 Matter 設備從特定的 Matter 控制器中移除（例如從 Apple Home 或 Google Home 中移除），可以按照以下步驟操作。
- 如果您將設備從某個控制器中移除，該控制器將無法再控制該設備。
  - 例如，如果您從 Google Home 中移除設備，該設備將不再顯示在 Google Home 中。
  - Apple Home 使用 Apple Keychain 保存配對信息。移除設備後，務必確保相關條目已被刪除，以避免潛在的連接問題。
- 如果需要，您隨後可以通過重新配對的方式，將該設備添加回其他控制器或平台。

```
1. 設定 --> 裝置與服務 --> Matter 整合卡片 --> 選擇「設備（Devices）」

2. 從設備列表中找到您希望移除的設備，點擊該設備以進入詳細頁面。

3. 管理設備的共享記錄（Fabrics）
   - 在「設備資訊（Device info）」部分，找到「共享設備（Share device）」按鈕。
   - 點擊其旁邊的「⋮」菜單，然後選擇「管理網絡（Manage fabrics）」。

4. 移除特定的控制器
   - 在「網絡（Fabrics）」列表中，找到您希望移除的控制器（例如 Google Home 或 Apple Home）。
   - 選擇該控制器，並點擊「移除（Remove）」以完成操作。
   - 針對 Apple Home 的額外步驟：
     - 如果您希望移除 Apple Home，還需要刪除 Apple 鑰匙串（Apple Keychain）中的條目。
     - 確保已經完全清除與 Apple Home 的關聯。
   - (補充) 從 HA 移除整個裝置
     - 點擊「⋮」菜單，然後選擇「刪除（Delete）」，這會將設備完全從 Home Assistant 中移除。
```
