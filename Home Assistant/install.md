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

# HA OS
