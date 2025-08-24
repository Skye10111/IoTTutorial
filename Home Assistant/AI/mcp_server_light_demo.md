# 範例規劃
- 透過 HA MCP Server 控制使用 MQTT 協議的燈光設備。
- **Home Assistant**
  - 安裝：docker container
  - 位置：`localhost:8123`
- **MQTT Broker**
  - 位置：`172.18.0.2:1883`
  - 使用者名稱、使用這密碼
- **加入 MQTT 整合**
  ```
  1. 設定 --> 裝置與服務 --> 新增「MQTT」整合
  2. 輸入 MQTT Broker 的 IP 和使用者帳密。
  ```
- **加入燈光設備**
  - topic
    - `bedroom/light/status`：讓 HA 查看燈光設備的開關狀態。
    - `bedroom/light/switch`：讓 HA 控制燈光設備的開關狀態。 
  ```
  1. 設定 --> 裝置與服務 --> MQTT --> 新增 MQTT 裝置。
  2. 輸入相關設置
  ```

# 建立 HA MCP server
- 加入 MCP server 整合
  ```
  設定 --> 裝置與服務 --> 新增「Model Context Protocol Server」整合
  ```
- 取得 Access Token
  ```
  1. 使用者頁面 --> 安全性分頁 --> 「永久有效存取權杖」區塊
  2. 新增權杖
  3. 複製權杖 (token) 的值 
  ```

# 建立 MCP client
- 安裝：`poetry add fastmcp dotenv`
### `.env`檔：
```
HA_PORT = 8123
HA_TOKEN = <你的 TOken>
```

### 在 `client.py`檔：
```
import os
import asyncio
import json
from fastmcp import Client
from dotenv import load_dotenv

# 載入 .env 檔
load_dotenv()
HA_PORT   = os.getenv("HA_PORT")   # str
HA_TOKEN  = os.getenv("HA_TOKEN") # str
serverURL = "http://localhost:{}/mcp_server/sse".format(HA_PORT)  # HA MCP server 的路徑

# 查看 HA MCP server 的 prompt
async def prompt():
    async with Client(
        serverURL,        # HA MCP server 的路徑
        auth = HA_TOKEN   # 貼上前面複製的 token 值
    ) as client:
        prompt = await client.get_prompt("Assist")
        print(prompt.messages[0].content.text)

# 查看設備狀態
async def getLiveContext():
    async with Client(
        serverURL, 
        auth = HA_TOKEN 
    ) as client:
        # 調用 tool
        toolName = "GetLiveContext"  # 查看設備狀態
        parameters = {}
        response = await client.call_tool(toolName, parameters)
        response = json.loads(response.content[0].text) # JSON 字串轉物件
        print(response["result"])

# 控制燈光開啟
async def hassTurnOn():
    async with Client(
        serverURL, 
        auth = HA_TOKEN 
    ) as client:
        # 調用 tool
        toolName = "HassTurnOn"  # 開啟燈光
        parameters = {
            "name"  : "mqttLight bedroom_light",  # 設備名稱
            "domain": ["light"]                   # 設備類型
        }
        response = await client.call_tool(toolName, parameters)

# 控制燈光關閉
async def hassTurnOff():
    async with Client(
        serverURL,     
        auth = HA_TOKEN 
    ) as client:
        # 調用 tool
        toolName = "HassTurnOff"  # 關閉燈光
        parameters = {
            "name"  : "mqttLight bedroom_light",  # 設備名稱
            "domain": ["light"]                   # 設備類型
        }
        response = await client.call_tool(toolName, parameters)


if __name__ == "__main__":
    # asyncio.run(prompt())
    # asyncio.run(hassTurnOn())
    # asyncio.run(hassTurnOff())
    # asyncio.run(getLiveContext())
```

### 看看 prompt 的內容
修改 `client.py` 檔：
```
if __name__ == "__main__":
    asyncio.run(prompt())
```
執行結果：
```
When controlling Home Assistant always call the intent tools. Use HassTurnOn to lock and HassTurnOff to unlock a lock. When controlling a device, prefer passing just name and domain. When controlling an area, prefer passing just area name and domain.
When a user asks to turn on all devices of a specific type, ask user to specify an area, unless there is only one device of that type.
This device is not able to start timers.
You ARE equipped to answer questions about the current state of
the home using the `GetLiveContext` tool. This is a primary function. Do not state you lack the
functionality if the question requires live data.
If the user asks about device existence/type (e.g., "Do I have lights in the bedroom?"): Answer
from the static context below.
If the user asks about the CURRENT state, value, or mode (e.g., "Is the lock locked?",
"Is the fan on?", "What mode is the thermostat in?", "What is the temperature outside?"):
    1.  Recognize this requires live data.
    2.  You MUST call `GetLiveContext`. This tool will provide the needed real-time information (like temperature from the local weather, lock status, etc.).
    3.  Use the tool's response** to answer the user accurately (e.g., "The temperature outside is [value from tool].").
For general knowledge questions not about the home: Answer truthfully from internal knowledge.

Static Context: An overview of the areas and the devices in this smart home:
- names: mqttLight bedroom_light
  domain: light
- names: 購物清單
  domain: todo
```
可以看到，我們的燈光設備的名稱是 `mqttLight bedroom_light`，`domain` 是 `light`。

### 查看所有設備狀態
修改 `client.py` 檔：
```
if __name__ == "__main__":
    asyncio.run(getLiveContext())
```
執行結果：
```
Live Context: An overview of the areas and the devices in this smart home:
- names: mqttLight bedroom_light
  domain: light
  state: 'off'
- names: 購物清單
  domain: todo
  state: '0'
```
可以看到，我們的燈光設備的名稱是 `mqttLight bedroom_light`，`domain` 是 `light`。

### 控制燈光開啟
修改 `client.py` 檔：
```
if __name__ == "__main__":
    asyncio.run(hassTurnOn())
```
執行後，燈光設備的 `bedroom/light/switch` topic 會被 HA 傳送 `ON` 訊息。

### 控制燈光關閉
修改 `client.py` 檔：
```
if __name__ == "__main__":
    asyncio.run(hassTurnOff())
```
執行後，燈光設備的 `bedroom/light/switch` topic 會被 HA 傳送 `OFF` 訊息。
