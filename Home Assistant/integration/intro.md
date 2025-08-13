# 簡介
Integration (整合) 是指一種用來**連接和整合外部服務、設備或平台**的模組。它使得 Home Assistant 能夠與其他智能家居設備、應用程式或服務進行互動，從而擴展其功能。
| 名詞 | 定義 | 關係 |
| --- | ---- | --- |
| **Integration** |  `整合`用來連接和管理外部平台、服務或設備的模組，包括設備的連接、數據的讀取和狀態更新。 | 每個`整合`都負責**提供 HA 與外部系統的接口**，並創建相關的`設備`或`實體`。 |
| **Device** |  `設備`代表一個具體的實體硬體設備，通常是指物理存在的智慧家庭設備。 | **`設備`是由某個`整合`創建，一個`設備`可以包含多個`實體`**。<br/>例如 1 個插座有 2 個 entity (開關控制 & 電力監測)。 |
| **Entity** | `實體`是 HA 中的基本單位，**代表一個具體的功能或狀態**。 | `實體`代表某個`設備`的特定屬性或功能，並提供狀態更新和控制操作。 |
| **Service** | `服務`是 HA 中用來**執行特定動作**的功能模組，例如打開燈光、調整溫度。 | `服務`執行操作時會針對某些`實體`，通常是由某個`整合`提供的。|
```
Integration  
  └── Device  
        └── Entity (1個設備可以有多個實體)   <-- (作用於) --  Service 
```

# 新增整合
以下是將 Integration 添加到 Home Assistant 的簡易步驟說明，並以 `Workday Integration` 作為示範。
```
1. 設定 --> 裝置與服務 --> 整合頁面 (進入後，預設就在此頁面)

2. 查看已安裝的 Integrations
   - 在「Integrations」頁面中，可以看到已安裝的所有 Integrations，部分是 Home Assistant 自動安裝的。

3. 檢查已發現的設備
   - 如果您的網路中有被 Home Assistant 自動偵測到的設備，這些設備將顯示在「已發現 (Discovered)」區域。
   - 若有設備被偵測到，可直接點擊「添加 (Add)」，並按照 UI 提示進行額外的設定。

4. 手動添加 Workday Integration
   - 如果沒有任何設備被自動偵測，您可以手動添加 Integration。
   - 點擊右下角的「新增整合 (Add Integration)」，搜索想要添加的 Integration (例如輸入 Workday )。

5. 配置 Workday Integration
   - 在彈出的窗口中，為 Integration 命名 (例如「明天是否是工作日 (Workday tomorrow)」)。
   - 選擇您的國家，用於判斷當地的節假日。

6. 進一步設定配置
   - 如果星期一不是工作日，可點擊「x」將其移除。
   - 若需檢查「明天是否是工作日」，在「偏移 (Offset)」中輸入數值 1。
   - 定義節假日和語言，並完成其他必要選項設置。

7. 選擇設備區域
   - 選擇 Integration 所屬的區域，例如「辦公室 (Office)」，然後點擊「完成 (Finish)」。

8. 檢查 Integration
   - 完成後，您可以在 Integrations 列表中看到剛添加的 Workday Integration。
```

# 查看 Integration  詳細資訊
```
1. 設定 (Settings) --> 設備與服務 (Devices & Services) --> 整合頁面 (Integrations)

2. 找到您已安裝的 Integration，點擊該 Integration，進入詳細資訊頁面。

3. 在 Integration 詳情頁面，點擊整合的『 ⋮ 』按鈕，可以看到：
   - 服務 (Service)：顯示該 Integration 提供的服務。
   - 實體 (Entity)：列出該 Integration 添加到 Home Assistant 的所有實體。

4a. 點擊「服務 (Service)」進入服務頁面，會會看到與該 Integration 提供的服務相關的詳細資訊。
   -「日誌 (Logbook)」區塊：查看該感測器的時間線。
   -「感測器 (Sensors)」區塊：
     - 可以看到該 Integration 提供的所有感測器（例如：工作日狀態）。
     - 如果您安裝的是其他類型的設備（例如氣候設備），可能會看到溫度、濕度、電池狀態等多個感測器。
   -「自動化 (Automations)」區塊
   -「場景 (Scenes)」區塊
   -「腳本 (Scripts)」區塊

4b. 點擊「實體 (Entity)」進入實體頁面，會看到該 Integration 添加到 HA 的所有實體列表。
   - 每個實體都有唯一的實體 ID，格式為『 domain.entity_name 』。
     - domain 表示設備類型，例如 sensor 或 light。
     - entity_name 是該實體的名稱。
```
點擊`服務 (Service)`進入服務頁面：
![image](https://www.home-assistant.io/images/getting-started/workday_service_info.png)
點擊`實體 (Entity)`進入實體頁面：
![image](https://www.home-assistant.io/images/getting-started/workday_entities_list.png)

# 修改 Integration 
### 重新命名 Integration
```
1. 找到您想要修改的 Integration（例如 Workday）。

2. 點擊「三點選單 (⋮)」 --> 選擇「重新命名 (Rename)」

3. 輸入新名稱並保存。
```

### 添加新的感測器 (Sensor)
- 如果需要添加另一個 Workday 感測器，例如用於查看同事的假期。
- 感測器 (Sensor) 屬於 Entity 的一種，專門用於提供數據或監控特定狀態。
  - 它不能直接控制（不像燈或開關那樣能開啟或關閉），只能用於提供資訊。
  - 例如：
    - 溫度感測器的 Entity ID 可能是 `sensor.temperature_outdoor`。
    - 工作日狀態的 Sensor 可能是 `sensor.workday_tomorrow`。 
```
1. 找到您想要修改的 Integration（例如 Workday）。

2. 點擊「新增條目 (Add entry)」。

3. 為新感測器命名，例如「同事假期 (Colleague holiday)」。

4. 定義相關選項，並保存設置。
```

