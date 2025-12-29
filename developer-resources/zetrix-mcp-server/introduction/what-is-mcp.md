# What is MCP

## **Model Context Protocol(MCP)**

The **MCP** is an open communication standard created by Anthropic to allow AI assistants to interact with external systems. It defines how tools are registered, invoked, and how data flows between an AI model and an external server.

#### **Key Concepts of MCP**

| Concept             | Explanation                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **Tool**            | A callable function exposed to the AI. The AI can execute these tools on behalf of the user (e.g., query blockchain state, generate keys). |
| **Transport Layer** | Defines how messages are exchanged between the AI client and MCP server (WebSocket, stdio).                                                |
| **Schema**          | Tool definitions, input/output formats, and error structures are clearly defined using JSON schemas.                                       |
| **Streaming**       | MCP supports both static responses and streaming events (e.g., block updates).                                                             |

#### **Why MCP Matters for Zetrix**

* Allows **direct AI access** to blockchain functions
* Eliminates manual scripting for common tasks
* Enables **conversational development workflows**
* Standardizes tool interface for reliability and safety
* Allows Claude to provide explanations, context, and corrections

In short, MCP transforms Zetrix development into an intelligent, assisted process.
