# Docker 安裝
- 使用瀏覽器訪問 `http://<你的伺服器 IP>:8123` 完成初始設定。
- 在 `/path/to/your/config` 存放 Home Assistant 的配置檔。
```
docker container run -d \
  -p 8123:8123 \
  --name homeassistant \  
  --restart=unless-stopped \  
  -v /path/to/your/config:/config \  
  homeassistant/home-assistant  
```
- (可選) 在 Container 安裝 HACS:
  - 進入容器：`docker exec -it <name of the container running homeassistant> bash`
  - 執行 HACS 下載腳本： `wget -O - https://get.hacs.xyz | bash -`
  - 重啟 Home Assistant
  - 設定 HACS 整合：
    ```
    1. 設定 --> 裝置與設備 --> 新增整合 --> 搜尋「HACS」

    2. Authenticate the integration：
   　　- HACS uses a device OAuth flow for authentication with GitHub
       - Copy the device code and select the link https://github.com/login/device

    3. Assign HACS to an 「area」 and select 「Finish」.
    ```

# HA OS
