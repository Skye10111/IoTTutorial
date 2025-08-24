# 核心配置文件 (`configuration.yaml`)
`!include customize.yaml` 是一條指令，告訴 Home Assistant 在該位置插入解析後的 `customize.yaml` 文件內容。被引入的文件內容必須是符合 YAML 語法的數據，並且在插入位置是合法的。
```
homeassistant:    
  name: "My Home Assistant Instance"    
  latitude: 37    
  longitude: -121    
  unit_system: metric   
  time_zone: "Asia/Taipei"    
  customize: !include customize.yaml   # 拆分的配置文件
```

# 嵌套 `!include` 指令
- `!include` 指令可以嵌套使用，意思是您可以**在被引入的文件中再次使用 `!include`**，這樣的層次化結構是完全支持的。
- 此外，某些 integrations 支持**多個頂層的 `!include` 指令**。
  - 這包括定義 IoT domain 的 integration (如：`light`、`switch`)，以及 `automation`、`script` 和 `template` 等 integration。
  - 只需為每個文件指定不同的標籤即可。

# 排查問題
### HA OS
- 透過 UI 檢查
  ```
  1. user profile --> enable Advanced Mode
  2. Developer tools --> YAML --> Configuration validation section  --> 點擊 Check configuration 按鈕
  ```
- 透過 cmd 檢查
  ```
  ha core check
  ```

### HA Container
```
# 運行完整檢查
docker exec homeassistant python -m homeassistant --script check_config --config /config

# 列出所有的 loaded files
docker exec homeassistant python -m homeassistant --script check_config --files

# 查看單一 integration 的配置（本例為 light）
docker exec homeassistant python -m homeassistant --script check_config --info light

# 查看所有 integration 的配置
docker exec homeassistant python -m homeassistant --script check_config --info all
```
