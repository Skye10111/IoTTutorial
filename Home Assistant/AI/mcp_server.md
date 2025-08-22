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

# HA 的 tool
### HassTurnOn 工具
  - `name` 屬性：**HassTurnOn**
  - `description` 屬性：
    ```
    Turns on/opens/presses a device or entity. For locks, this performs a 'lock' action. Use for requests like 'turn on', 'activate', 'enable', or 'lock'.
    ```
  - `inputSchema` 屬性：
    ```
    {
      'type': 'object',
      'properties': {
        'name'        : { 'type': 'string'},
        'area'        : { 'type': 'string'},
        'floor'       : { 'type': 'string'},
        'domain'      : { 'type': 'array', 'items': {'type': 'string'} },
        'device_class': {
          'type': 'array',
          'items': {
            'type': 'string',
            'enum': [
              'awning', 'blind', 'curtain', 'damper', 'door', 'garage', 'gate', 'shade', 'shutter', 'window',
              'identify', 'restart', 'update', 'tv', 'speaker', 'receiver', 'water', 'gas', 'outlet', 'switch'
            ]
          }
        }
      }
    }
    ```
### HassTurnOff 工具
  - `name` 屬性：**HassTurnOff**
  - `description` 屬性：
    ```
    Turns off/closes a device or entity. For locks, this performs an 'unlock' action. Use for requests like 'turn off', 'deactivate', 'disable', or 'unlock'.
    ```
  - `inputSchema` 屬性：
    ```
    {
      'type': 'object',
      'properties': {
        'name'  : { 'type': 'string' },
        'area'  : { 'type': 'string' },
        'floor' : { 'type': 'string' },
        'domain': { 'type': 'array', 'items': {'type': 'string'} },
        'device_class': {
          'type'   : 'array',
          'items'  : {
            'type' : 'string',
            'enum' : [
              'awning', 'blind', 'curtain', 'damper', 'door', 'garage', 'gate', 'shade', 'shutter', 'window',
              'identify', 'restart', 'update', 'tv', 'speaker', 'receiver', 'water', 'gas', 'outlet', 'switch'
            ]
          }
        }
      }
    }
    ```

### HassCancelAllTimers 工具
- `name` 屬性：**HassCancelAllTimers**
- `description` 屬性：
  ```
  Cancels all timers
  ```
- `inputSchema` 屬性：
  ```
  {
    'type': 'object',
    'properties': {
      'area': {'type': 'string'}
    }
  }
  ```

### HassListAddItem 工具
- `name` 屬性：**HassListAddItem**
- `description` 屬性：
  ```
  Add item to a todo list
  ```
- `inputSchema` 屬性：
  ```
  {
    'type': 'object',
    'properties': {
      'item': {'type': 'string'},
      'name': {'type': 'string'}
    }
  }
  ```

### HassListCompleteItem 工具
- `name` 屬性：**HassListCompleteItem**
- `description` 屬性：
  ```
  Complete item on a todo list
  ```
- `inputSchema` 屬性：
  ```
  {
    'type': 'object',
    'properties': {
      'item': {'type': 'string'},
      'name': {'type': 'string'}
    }
  }
  ```

### HassLightSet 工具
- `name` 屬性：**HassLightSet**
- `description` 屬性：
  ```
  Sets the brightness percentage or color of a light
  ```
- `inputSchema` 屬性：
  ```
  {
    'type': 'object',
    'properties': {
      'name'       : {'type': 'string'},
      'area'       : {'type': 'string'},
      'floor'      : {'type': 'string'},
      'domain'     : {'type': 'array', 'items': {'type': 'string'}},
      'color'      : {'type': 'string'},
      'temperature': {'type': 'integer', 'minimum': 0},
      'brightness': {
        'type'       : 'integer',
        'minimum'    : 0,
        'maximum'    : 100,
        'description': 'The brightness percentage of the light between 0 and 100, where 0 is off and 100 is fully lit'
      }
    }
  }
  ```

### todo_get_items 工具
- `name` 屬性：**todo_get_items**
- `description` 屬性：
  ```
  Query a to-do list to find out what items are on it. Use this to answer questions like 'What's on my task list?' or 'Read my grocery list'.
  Filters items by status (needs_action, completed, all).
  ```
- `inputSchema` 屬性：
  ```
  {
    'type': 'object',
    'properties': {
      'todo_list': { 'type': 'string', 'enum': ['購物清單'] },
      'status': {
        'type': 'string',
        'enum': ['needs_action', 'completed', 'all'], 
        'description': 'Filter returned items by status, by default returns incomplete items',
        'default': 'needs_action'
      }
    }
  }
  ```

### GetLiveContext 工具
- `name` 屬性：**GetLiveContext**
- `description` 屬性：
  ```
  Provides real-time information about the CURRENT state, value, or mode of devices, sensors, entities, or areas.
  Use this tool for:
  1. Answering questions about current conditions (e.g., 'Is the light on?').
  2. As the first step in conditional actions (e.g., 'If the weather is rainy, turn off sprinklers' requires checking the weather first).
  ```
- `inputSchema` 屬性：
  ```
  {
    'type'      : 'object',
    'properties': {}
  }
  ```
  
