# 建立 HA 的 MCP server
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

# 連接 HA 的 MCP server
```
import asyncio
from fastmcp import Client

async def run():
    async with Client(
        "http://localhost:8123/mcp_server/sse",   # HA MCP server 的路徑
        auth = <access token>                     # 貼上前面複製的 token 值
    ) as client:
        # 取得 HA 可用的工具
        tools = await client.list_tools()

        # 取得 HA 可用的 prompts
        prompts = await client.list_prompts()

        # 取得 prompt
        prompt = await client.get_prompt("Assist")

        # 使用 tool
        result = await client.call_tool(
            "工具名稱",
            { ... }      # tool 使用參數
        )

if __name__ == "__main__":
    asyncio.run(run())
```

# HA 的 Prompt
使用 `await client.get_prompt("Assist")` 取得 HA 的 prompt。
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
- names: ...
  domain: ...
- names: ...
  domain: ...
```

