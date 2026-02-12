# MCP — Model Context Protocol

## Qué es MCP

Un estándar abierto de Anthropic para conectar LLMs con tools externos, fuentes de datos y servicios. Piensa en ello como **USB-C para IA** — un protocolo estándar para conectar con cualquier cosa.

```
LLM (Claude, GPT, etc.)
  ↕ MCP Protocol
MCP Server (your tools, databases, APIs)
```

## Por qué importa

Sin MCP: cada integración es código custom, acoplado a un solo proveedor.
Con MCP: defines tus tools una vez, y cualquier cliente compatible con MCP puede usarlos.

## Conceptos principales

- **MCP Server:** Expone tools, resources y prompts a través de un protocolo estándar
- **MCP Client:** Cualquier aplicación que se conecta a MCP servers (Claude Desktop, Claude Code, IDEs)
- **Tools:** Funciones que el LLM puede llamar (igual que function calling, pero estandarizado)
- **Resources:** Datos que el LLM puede leer (ficheros, registros de base de datos, respuestas de APIs)

## Construir un MCP Server

```ts
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-company-tools",
  version: "1.0.0",
});

// Define a tool
server.tool(
  "search_products",
  "Search the product catalog",
  {
    query: z.string().describe("Search query"),
    category: z.string().optional().describe("Product category"),
  },
  async ({ query, category }) => {
    const results = await db.searchProducts(query, category);
    return {
      content: [{ type: "text", text: JSON.stringify(results) }],
    };
  }
);

// Start server
const transport = new StdioServerTransport();
await server.connect(transport);
```

## Enfoque de consultoría

MCP es valioso para clientes enterprise porque:

1. **Integración con tools internos** — conecta LLMs a Slack, Jira, DBs internas
2. **Estandarizado** — no estás atado a un solo proveedor de IA
3. **Seguro** — los tools corren en la infraestructura del cliente, los datos no salen
4. **Composable** — añade/elimina capacidades sin cambiar el código del LLM

Ejemplo de pitch: "Puedo construir MCP servers que conecten Claude/GPT con tus sistemas internos — CRM, base de datos, ticketing — para que tu equipo pueda consultar datos de la empresa en lenguaje natural."

## Fuentes

- [MCP Official Docs](https://modelcontextprotocol.io/)
- [MCP GitHub — TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)
- [Anthropic — MCP Announcement](https://www.anthropic.com/news/model-context-protocol)
- [MCP — Example Servers](https://github.com/modelcontextprotocol/servers)
