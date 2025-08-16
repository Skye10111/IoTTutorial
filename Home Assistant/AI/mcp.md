# 簡介
Home Assistant 作為一個全能的開源智慧家庭平台，它也內建了 MCP 能力，可以分別扮演 2 種角色：
| 角色 | 說明 |
| --- | ---- |
| **MCP server** | 將 Home Assistant 中的**設備控制功能**（如燈光、空調、感測器）作為 MCP 接口，供外部大模型（如「小智」）使用。 |
| **MCP client**| **接入外部 MCP Server**，結 Home Assistant 裡已接入的 LLM，進一步增強 HomeAssistant Assist 本身作為「大模型 Agent」時的能力 |

