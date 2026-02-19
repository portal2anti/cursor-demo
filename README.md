# cursor-demo

Demo repository for Cursor and the Model Context Protocol (MCP).

---

## Model Context Protocol (MCP)

**Model Context Protocol** is an open protocol that lets AI applications connect to external data sources and tools in a standard way. It was introduced by Anthropic and is supported by Cursor, Claude, and other AI tools.

### What MCP Does

- **Tools** – Expose actions the model can call (e.g. create a repo, read a file, run a search).
- **Resources** – Expose data the model can read (e.g. docs, APIs) via a uniform URL scheme.
- **Prompts** – Provide reusable prompt templates that can be filled with context.

Servers implement MCP and expose these capabilities; clients (like Cursor) connect to servers and use them during the conversation.

### Why It Matters

- **Standard interface** – One protocol for many tools and data sources.
- **Composability** – Swap or add servers without changing the client.
- **Safety** – The client controls which servers are enabled and what they can do.
- **Ecosystem** – Community and vendors can build and share MCP servers (e.g. GitHub, databases, APIs).

### How Cursor Uses MCP

In Cursor, MCP servers are configured (e.g. in **Cursor Settings → MCP** or project `.cursor/mcp.json`). Once a server is running, Cursor can:

- Call its **tools** when answering your questions or doing tasks.
- Read **resources** when you reference them.
- Use **prompts** when they are applicable.

So “using the GitHub MCP” in Cursor means Cursor is calling tools (e.g. create repo, create/update file) provided by a GitHub MCP server on your behalf.

### Learn More

- [Model Context Protocol (Anthropic)](https://modelcontextprotocol.io/)
- [MCP specification and docs](https://spec.modelcontextprotocol.io/)
- [Cursor MCP setup](https://docs.cursor.com/context/model-context-protocol)
