# 簡介
Home Assistant 作為一個全能的開源智慧家庭平台，它也內建了 MCP 能力，可以分別扮演 2 種角色：
| 角色 | 說明 |
| --- | ---- |
| **MCP server** | 將 Home Assistant 中的**設備控制功能**（如燈光、空調、感測器）作為 MCP 接口，供外部大模型（如「小智」）使用。 |
| **MCP client**| **接入外部 MCP Server**，結 Home Assistant 裡已接入的 LLM，進一步增強 HomeAssistant Assist 本身作為「大模型 Agent」時的能力 |

### HA 作為 MCP client
Home Assistant 內建**語音助理**的工作邏輯（自己是 Agent 接取 LLM + MCP server）
![image](./mcp_client.png)

### HA 作為 MCP server

- **伺服器發送事件 (Server-Sent Events)**
  - SSE 是一種 Web 技術，允許伺服器向客戶端(通常是瀏覽器) 推送即時更新。
  - SSE 使用 HTTP 協議，**伺服器只需開啟一個 HTTP 連接，然後持續向客戶端傳送數據，而不需要客戶端不斷輪詢**。
  - SSE 適用於各種需要即時資料推播的場景，例如股票行情更新、社群媒體通知等。
  - 與 WebSocket 相比，SSE 更加簡單易用，但它是**單向通信**，只能由伺服器向客戶端推送資料。

