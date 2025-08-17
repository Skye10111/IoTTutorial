# 簡介
- Matter 是一種全新的**智能家居連接標準**，用於家居自動化產品和物聯網（IoT）設備。
- Matter 設備運行於本地，始終支持**本地控制**，設備控制**不需要任何互聯網連接或雲服務**。
  - 然而，有些廠商可能需要您設置賬戶才能啟用其產品的 Matter 支持（尤其是那些商業廠商的專屬網關/橋接器/集線器/控制器等設備）。
- 與其他常見的基於無線電的物聯網協議（如 Zigbee、Z-Wave 和藍牙）不同：
  - Matter 標準規範本身並不包含其專有的無線電協議或網絡傳輸協議。相反，它是一種運行於現有網絡基礎設施之上的應用層服務控制協議。
  - **所有 Matter 設備均使用標準基於 IP（IPv6）的通信，通過現有的局域網（例如 Wi-Fi 和以太網）或Thread（低功耗無線個人區域網）進行通信**，具體取決於設備類型。
 
### Thread 與 Matter 的關係
Thread 是一種**低功耗的無線射頻網狀網絡技術**。它與 Zigbee 相似，但關鍵區別在於 Thread 是 **IP 可尋址**的，這使它成為 Matter 的一個適合的傳輸協議選擇。
- Matter 使用 Thread 作為其傳輸協議之一，Thread 的 IP 可尋址特性與 Matter 的 IP 基礎架構完美匹配。
- Border Router
  - Thread 網絡中的「邊界路由器」（Border Router）負責將 Thread 網絡內的設備通信流量轉發至網絡外的設備。
  - 換句話說，它充當 Thread 網絡和其他網絡（例如 Wi-Fi 或以太網）的橋梁，實現不同網絡之間的通信。
  - 它能夠確保 Thread 網絡中的設備可以與其他 Matter 兼容設備以及控制器（如 Home Assistant）進行通信。

![image](https://www.home-assistant.io/images/integrations/matter/matter_thread_infographic.png)

### Thread 設備並不一定支持 Matter
雖然 Thread 設備可以使用 Matter 作為控制協議，但**並非所有基於 Thread 的設備都支持 Matter**。有些 Thread 設備可能使用其他協議，例如 Apple HomeKit 或廠商專屬的通信協議。
- 辨別 Matter 支持的設備
  - Thread 技術與 Matter 協議是獨立的，Thread 標誌僅表示該設備支持 Thread 網絡通信。
  - 尋找設備上的 Matter 標誌，該標誌可以出現在基於 Wi-Fi、以太網或 Thread 的設備上。
<table>
  <tr>
    <td><b>Thread</b></td>
    <td>低功耗的網狀網絡技術，用於設備之間的無線通信。</td>
  </tr>
  <tr>
    <td><b>Matter</b></td>
    <td>基於 IP 的控制協議，旨在標準化智能家居設備的互操作性。</td>
  </tr>
</table>

# 使用藍牙進行設備加入（Commissioning）
- 大多數（如果不是全部）符合 Matter 標準的設備都內置藍牙芯片，用於簡化設備的加入過程（Commissioning）。
- 藍牙的用途：
  - 藍牙**僅用於設備的加入（配對）**，而不是用於控制設備。
  - 在設備開箱或恢復出廠設置後，可以使用藍牙完成設備的初始配對。
- Home Assistant 的加入流程：
  - HA 的 Matter 控制器使用 Home Assistant Companion App 來進行設備的加入。
  - 在加入過程中，需要將手機靠近 Matter 設備。
    - 控制器會通過藍牙將您的網絡憑證（例如 Wi-Fi 或 Thread 網絡信息）傳送至設備。
    - 設備完成加入後，將不再使用藍牙，而是通過其本地接口（例如 Wi-Fi 或 Thread）進行通信。

# Multi Fabric 功能：加入多個控制器
- Matter 的一個重要特性是所謂的 Multi Fabric 功能，該功能**允許同一設備同時加入多個控制器**。
  - 這意味著您可以將一個 Matter 設備同時添加到 Google Home、Apple Homekit 和 Home Assistant 等多個生態系統中。
  - `Fabric` 在 Matter 技術中指的是一個由特定控制器（或控制系統）管理的網絡。
  - Matter 標準規定，每個設備至少應支持同時連接 5 個不同的 Fabric。
- Matter 的 Multi Fabric 功能讓用戶能更靈活地在多個生態系統中使用同一設備，提供了便利性和互操作性。
  - 然而，對於一些擁有豐富專屬功能的設備，廠商的原生整合可能是更好的選擇。 

# 推薦安裝：Home Assistant OS
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
