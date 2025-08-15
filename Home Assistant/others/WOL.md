# 簡介
Wake-on-LAN 是通過在有線以太網（Ethernet）中發送特殊的「魔術封包」（Magic Packet）來喚醒處於休眠或關機狀態的設備。這些封包需要設備的網卡（Network Interface Card, NIC）在低功耗模式下仍然能夠接收和處理網絡封包。
| 功能 | 做法 |
| --- | ---- |
| 開機 | **網路喚醒 (WOL, Wake On Lan)** 功能可以將電腦從低功率狀態 (睡眠、休眠及關機）遠端喚醒。 |
| 關機 | 使用 password-less 的 **SSH** 下達關機指令給電腦。 |


# 確認主機板是否支持 WOL 功能
- 確認主機板型號：
  ```
  sudo dmidecode -t baseboard

  # ...
  # Base Board Information
  # Manufacturer: ASUSTeK COMPUTER INC.
  # Product Name: TUF GAMING B760-PLUS WIFI    <-- 這裡顯示主機板型號
  # ...
  ```
- 官網查看主機板 BIOS 是否支持 WOL
  ```
  # 以 ASUS TUF GAMING B760-PLUS WIFI 為例

  1. 首先請至華碩官網點擊查詢「TUF GAMING B760-PLUS WIFI」
  2. 點擊 [技術規格/產品規格] 頁面
  3. 找到 [管理能力 (Manageability)]，顯示 WOL by PME 是支援網路喚醒功能。
  ```
- BIOS 設置啟用網路喚醒(WOL)
  ```
  1. 電腦開機後，立刻按壓鍵盤上的 “delete” 鍵，進入 BIOS。
  2. 按壓鍵盤 F7 鍵，進入 [Advanced Mode]。
  3. 在 [Advanced Mode] 進入 [Advance] 分頁 --> 找到 [APM Configuration]。
  4. 點選 [Power On By PCI-E] 選項並設置為 [Enabled]。
  5. 按壓鍵盤 F10 鍵，點選 Ok，保存選項，電腦重啟後，啟用網路喚醒功能。 
  ```

# SSH 預先設置
- 通常使用關機指令關閉 Linux 遠端機器需要 root 使用者存取權限，顯然我們**不想在出現安全漏洞等情況下**授予 Home Assistant 完全 root 存取權。因此，進行以下操作。
- **在遠端主機為 Home Assistant 建立一個新用戶**
  - 即：之後 HA 會使用這個身份在 SSH 下達關機指令給遠端主機。
  ```
  sudo adduser homeassistant
  
  # 接著填寫所需信息 (包括密碼)，稍後會用到。
  ```
- **授予 homeassistant 使用者有 reboot 的權限** (編輯 `sodoers` 檔案)：
  ```
  sudo visudo

  # sudoers 檔案現在應該已經用 visudo 打開，它會驗證檔案的語法。
  # 在 user privilege specification (使用者權限規格) 下方新增一行，為新建立的 homeassistant 使用者新增存取權限。
  # User privilege specification
  homeassistant ALL=(ALL) NOPASSWD: /sbin/poweroff, /sbin/reboot, /sbin/shutdown
  ```
  完成後，儲存 (`ctrl + O`) 並退出 (`ctrl + X`)。
- **建立 SSH key pair**
  - 在 HA 所在主機，生成 public & private 金鑰：
    ```
    ssh-keygen -t rsa -f ~/.ssh/id_rsa_homeassistant
  
    # 詢問密碼時，請將其留空。
    # 如果輸入了密碼，SSH 連線將需要金鑰和密碼，並且無法用於家庭助理的遠端命令。
    # 公鑰檔：~/.ssh/id_rsa_homeassistant.pub  --> 要移到遠端主機的 /home/homeassistant/.ssh 目錄下
    # 私鑰檔：~/.ssh/id_rsa_homeassistant
    ```
  - 把 public key 傳給遠端主機：
    ```
    ssh-copy-id -i ~/.ssh/id_rsa_homeassistant.pub homeassistant@<遠端主機 IP>
    ```
  - 完成後測試：
    - 無密碼登入遠端主機：`ssh -i ~/.ssh/id_rsa_homeassistant homeassistant@<遠端主機 IP>`
    - 無密碼重新啟動遠端主機：`ssh -i ~/.ssh/id_rsa_homeassistant homeassistant@<遠端主機 IP> sudo reboot`
    
# Home Assistant 設定  
### 加入 WOL (Wake On Lan)) 整合

### 加入 WOL 裝置

### 設置 switch 卡片

https://www.creatingsmarthome.com/index.php/2022/02/12/guide-start-up-and-shut-down-remote-linux-pc-using-home-assistant/
