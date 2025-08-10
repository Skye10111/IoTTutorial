# 使用 YAML 修改「總覽儀表板」
- 要更改總覽儀表板，請在 HA 的配置目錄 (`/config`) 中：
  - 創建一個新的文件 `ui-lovelace.yaml`。
  - 在 `configuration.yaml` 文件中新增以下部分。
    ```
    lovelace:
      mode: yaml
    ```
- 然後重新啟動 Home Assistant。
- ${\text{{\color{red}一旦通過 YAML 接管 UI 的控制，HA 的介面將不再提供修改儀表板的功能，新增的實體也不再自動出現在此 UI 中。}}}$

### 建立 `ui-lovelace.yaml` 的初始內容
- 從 UI 中**複製貼上 “原始配置”（Raw Configuration）**，這樣您的手動配置就會與現有的 UI 配置保持一致。
- 獲取現有儀表板的原始配置
  ```text
  1. 在側邊欄中選擇「總覽」儀表板。
  
  2. 點擊右上角的 pencil 按鈕 --> ⋮ 按鈕 --> 「以 YAML 編輯」按鈕

  3. 您將看到目前儀表板的配置。將其複製貼上到 ui-lovelace.yaml 中。
  ```
  - 

 
