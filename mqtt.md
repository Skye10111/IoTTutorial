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

# MQTT 實際使用
### 運行 MQTT Broker (Mosquitto)
- 下載 image：`docker pull eclipse-mosquitto`
- 建立 volume 掛載目錄：
  ```
  sudo mkdir -p /mosquitto/config  # 存放設定檔的目錄
  sudo mkdir -p /mosquitto/data    # 存放數據檔的目錄
  sudo mkdir -p /mosquitto/log     # 存放日誌檔的目錄
  ```
- 建立設定檔 (`/mosquitto/config/mosquitto.conf`)
  ```
  persistence true
  persistence_location /mosquitto/data/
  log_dest file /mosquitto/log/mosquitto.log
  
  allow_anonymous true

  listener 1883
  protocol mqtt
  ```
  | 參數 | 說明 |
  | --- | ---- |
  | `persistence` | `true` 為開啟資料存放在地端，讓通訊更穩定。 |
  | `persistence_location` | 指定資料存放於 /mosquitto/data/ 目錄下。 |
  | `log_dest file` | Log 會被寫到 /mosquitto/log/mosquitto.log 檔案內。 |
  | `allow_anonymous` | 在 Mosquitto 2.0 之後預設不允許匿名使用者登入，因此要設定此選項為 `true` 才可匿名登入。 |
  | `listener` | 掛載服務於 1883 Port。 |
  | `protocol` | 設定  MQTT 使用 1883 Port。 |
- 運行 MQTT Container
  - `-p 1883:1883`： 將 MQTT 的 TCP 預設端口 (1883) 映射到主機。
  - `p 9001:9001`：（可選) 開啟 WebSocket 支援的端口。 
  ```
  docker run -d \
    --name mqtt \
    -p 1883:1883 \
    -v /mosquitto:/mosquitto \
    eclipse-mosquitto
  ```
### 運行 MQTT Client
- Mosquitto 的 Docker 映像已包含 MQTT 客戶端工具（`mosquitto_pub` 和 `mosquitto_sub`）。
- 先啟動一個  `eclipse-mosquitto` container，讓 `mosquitto_pub` 和 `mosquitto_sub` 都在裡面溝通。
  ```
  docker container run -d --name mqtt -p 1883:1883 -v /mosquitto:/mosquitto eclipse-mosquitto
  ```
- **訂閱消息 (Subscriber)**
  - 在 `A 終端` 執行以下指令，作為 Subscriber：
    - 當 Publisher 發送消息時，Subscriber 會接收到並顯示。
    ```
    docker container exec mqtt mosquitto_sub -h <broker_ip> -t "test/topic"  
    ```
  - 訂閱主題（Topic）可以是：
    - 完整的名稱。
    - 可搭配萬用字元，訂閱單層或者多層相關主題。
      - `+`：匹配**單一階層**的主題名稱 (e.g. `home/+/temperature`、`home/+/+`)。
      - `#`：匹配**多層**主題名稱，這個字元只放在名稱最後 (e.g. `home/#`)。
    
    | 參數 | 說明 |
    | --- | ---- |
    | `-h` | MQTT Broker 的 IP 地址（例如 `localhost` 或容器的 IP）。 |
    | `-t` | Topic 名稱。 |
- **發佈消息 (Publisher)**
  - 在 `B 終端` 執行以下指令，作為 Publisher：
    ```
    docker container exec mqtt mosquitto_pub -h <broker_ip> -t "test/topic" -m "Hello MQTT from Docker"
    ```    
    | 參數 | 說明 |
    | --- | ---- |
    | `-m` | 發送的消息內容。 |
    | `-r`| 保留 (Retain) 發布訊息，MQTT代理人將保存此主題訊息。<br/>其後如有新的訂閱者，或者之前斷線的訂閱者重新連線，都能收到最新 1 則保留訊息。 |

### 使用 MQTT 帳密
- Mosquitto 提供了一個工具 `mosquitto_passwd`，可以用來生成帳密檔案。
- 創建帳密檔
  ```
  docker container exec <broker_container_id> mosquitto_passwd -c /mosquitto/passwordfile <username>
  ```
  | 參數 | 說明 |
  | --- | ---- |
  | `-c` | 創建新的帳密檔案 (如果檔案已存在，會覆蓋原有檔案)。<br/>若只是想新增使用者到已經存在的帳密檔，不加此選項即可。 |
  | `/mosquitto/passwordfile` | 帳密檔案的路徑。<br/>這裡假設 Mosquitto 容器內的目錄 `/mosquitto` 是可寫的。 |
  | `<username>` | 用戶名稱，執行指令後會輸入密碼。 |
- 確認配置檔 (`/mosquitto/config/mosquitto.conf`) 已經指向這個帳密檔案。
  ```
  allow_anonymous false  
  password_file /mosquitto/passwordfile  
  ```
- 如果你修改了 Mosquitto 的配置檔案或新建了帳密檔案，可能需要重新啟動容器以應用更改。
  ```
  docker container restart <broker_container_id>  
  ```

### 建立 ACL 檔
- ACL (Access Control List) 存取控制表是用來描述一個物件對一串列的存取權限，Mosquitto 同樣也提供這個功能。
- 創建一個 ACL 檔 (`/mosquitto/config/acl`)
  - 使用者 joker 只可以讀寫符合 pattern 為 `joker/#` 的 topic。
  ```
  user joker
  topic readwrite joker/#
  ```
- 確認配置檔 (`/mosquitto/config/mosquitto.conf`) 已經指向這個帳密檔案。
  ```
  allow_anonymous false
  password_file /mosquitto/config/passwd_file
  acl_file /mosquitto/config/acl  # 加上這行
  ```
