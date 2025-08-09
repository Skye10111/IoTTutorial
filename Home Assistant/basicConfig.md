# 配置基本資訊
修改主配置文件 `configuration.yaml`，添加以下內容：
```
homeassistant:  
  name: My Smart Home  
  latitude: 25.0330  
  longitude: 121.5654  
  elevation: 10  
  unit_system: metric  
  time_zone: Asia/Taipei  
  external_url: "http://<主機IP>:8123"  
  
# 啟用核心功能  
default_config:  
  
# 日誌記錄  
logger:  
  default: info
```
| 參數 | 說明 |
| --- | ---- |
| `latitude`<br/>`longitude`<br/>`elevation` | 所在位置的經緯度座標、海拔高度，影響天氣預測。 |
| `unit_system` |  設為 metric 使用公制（如 °C、公里）。 |
| `default_config` | 啟用預設功能，包括：設備自動發現、日誌記錄、界面設置等。 |

# 初次啟動與登入
- 啟動 Docker 容器後，訪問 `http://<主機IP>:8123`。
- 第一次登錄時，需要設定：**帳號**（管理員帳號）、**位置**（用於天氣、日出日落等功能）、**時區**（例如 Asia/Taipei）、**是否加入匿名統計**（可選）。

# 加入 MQTT Broker
- 進到控制面板頁面，進到路徑：
  ```
  左側側邊欄 --> 設定 --> 裝置與服務 --> 右下角的「新增整合」--> 輸入「MQTT」
  ```
- 設定以下欄位： 
  - Broker (ip 位址)、通訊埠 (`1883`)
  - 使用者名稱、密碼
