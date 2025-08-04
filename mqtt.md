# 簡介
- MQTT 和 HTTP 的底層都是 **TCP/IP**，也就是物聯網裝置可以沿用既有的網路架構和設備，只是在網路上流通的「訊息格式」以及應用程式的處理機制不同。
- 不同於 HTTP 的標頭 (header) 採用文字描述，**MQTT 的標頭採用數字編碼**，整個長度只佔 2 bytes，等同 2 個字元，後面跟著訊息的主題 (topic)和內容（payload）。
- MQTT是一種基於**發布/訂閱機制**的訊息傳輸協定。
  - **代理人（broker)**（server）
    - 是個伺服器軟體，可儲存發布者 (publisher) 的訊息。
    - 在 publisher 中斷連線的情況下，提供 subscriber 最近更新的訊息。
  - **發布者 (publisher)**（client）：
    - 向伺服器發送主題 (topic) 的一方。
  - **訂閱者 (subscriber)**（client）：
    - 從伺服器獲取主題的一方。
    - subscriber 需要告知 broker 想要訂閱的 topic。
  - 一台裝置可以同時是publisher和subscriber。
  - ${\text{{\color{red}由於 2 個 client 端之間有伺服器當作中繼站，所以兩邊並不需要知道彼此的 IP 位址。}}}$
 

# MQTT 訊息格式
```
--------------------------------------  
| Fixed Header                       |  
| - Message Type (4 bits)            |  
| - Flags (4 bits)                   |  
| - Remaining Length (1 ~ 4 bytes)   |  
-------------------------------------- 
| Variable Header (variable length)  | 
--------------------------------------  
| Payload (variable length)          | 
--------------------------------------  
```
- **固定標頭 (Fixed Header)**
  - MQTT 協議規定固定標頭由**1 個控制位元組 + 1 ~ 4 個剩餘長度位元組**構成。
  - **第 1 個 byte**：包含訊息的控制欄位 (Control Byte)，用來表示訊息類型和一些旗標（Flags）。 
    | 資訊 | 大小 | 說明 |
    | --- | ---- | --- |
    | **訊息類型<br/>(Message Type)** | 4 bits | 用來指示 MQTT 訊息的類型，例如 `CONNECT`、`PUBLISH`、`SUBSCRIBE` 等。 |
    | **訊息標誌<br/>(Flags)** | 4 bits | 用來表示訊息的特定屬性，例如 `DUP`(是否為副本)、`QoS`(傳輸品質) 等。 |
  - **剩餘長度 (Remaining Length)**
    - 使用 1 ~ 4 個 bytes 表示，根據後續內容的大小動態決定需要多少 bytes 來表示剩餘長度。 
    - 剩餘長度表示 MQTT 訊息中 固定標頭之外的所有內容 (Variable Header 和 payload) 的總 bytes 數。。
- **可變標頭 (Variable Header)**
  - 根據不同的訊息類型，可能包含以下資訊。
    | 資訊 | 說明 |
    | --- | ---- |
    | **主題名稱<br/>(Topic Name)**| 用於 `PUBLISH` 訊息，指定訊息的目的地主題。 |
    | **封包識別碼<br/>(Packet Identifier)**| 用於 `QoS` > 0 的訊息，標識封包。 |
    | 其他欄位 | 例如 `CONNECT` 訊息的「協議名稱」、「協議版本」等。 |
- **內容 (Payload)**
  - Payload 包含**訊息的主要內容**，根據不同的訊息類型會有所不同。例如：
    - 在 `PUBLISH` 訊息中，payload 是需要傳遞的數據。
    - 在 `SUBSCRIBE` 訊息中，payload 包含要訂閱的主題。

### MQTT的主題（Topic）名稱 
- MQTT主題（topic）名稱是 UTF-8 編碼的字串，我們可以自行決定主題名稱。
- 根據 MQTT 協議（例如 MQTT 3.1.1 和 MQTT 5.0），主題名稱必須符合以下要求：
  - 主題名稱是 UTF-8 編碼的字串。
  - 主題名稱可以包含任意的字元，包括特殊字元（如空白鍵）。
  - 主題名稱不能為空字串（""），至少需要包含一個字元。
  - 主題名稱的最大長度為 65535 bytes。
- 主題名稱也支援類似檔案路徑的**階層式命名**方式，每個階層之間用 `/` 分隔。
  - 假設住家裡面有許多感測器，我們可依照測器所在位置，規劃如下的命名階層結構：
    ```
    - home
      - 庭院(yard)
        - 人體感測器        --> home/yard/PIR
        - 溫度感測器        --> home/yard/temperature
      - 客廳 (living room)
        - 溫度感測器        --> home/livingRoom/temperature
    ```
  - 當同一個地點包含許多感測器的時候，用編號或者唯一識別碼來命名主題是比較合理的選擇。
    - 例如，某個廚房的裝置的 MAC 位址是 `DEADBEEFFEED`，它可以被命名成 `Home/kitchen/DEADBEEFFEED`。




