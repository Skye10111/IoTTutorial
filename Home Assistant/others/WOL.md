# 簡介
**網路喚醒 (WOL, Wake On Lan)** 功能允許您透過網路喚醒睡眠或休眠中的電腦。

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

https://www.creatingsmarthome.com/index.php/2022/02/12/guide-start-up-and-shut-down-remote-linux-pc-using-home-assistant/
https://www.asus.com/tw/support/faq/1045950/
https://www.asus.com/tw/motherboards-components/motherboards/tuf-gaming/tuf-gaming-b760-plus-wifi/techspec/
