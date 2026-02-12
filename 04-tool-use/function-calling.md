# Function Calling / Tool Use

## Qué es

El LLM no solo genera texto — puede **decidir llamar a funciones** que tú defines. El LLM elige qué tool usar y qué argumentos pasar, pero ERES TU quien ejecuta la función.

```
User: "What's the weather in Tokyo?"
  → LLM sees available tools → decides to call get_weather(city: "Tokyo")
  → YOU execute get_weather("Tokyo") → return result to LLM
  → LLM: "It's currently 15°C and sunny in Tokyo."
```

**El LLM nunca ejecuta código.** Solo decide qué llamar y con qué parámetros.

## Claude API — Tool Use

### 1. Definir Tools

```ts
const tools = [
  {
    name: "get_weather",
    description: "Get current weather for a city",
    input_schema: {
      type: "object",
      properties: {
        city: { type: "string", description: "City name (e.g., 'Tokyo', '東京')" },
      },
      required: ["city"],
    },
  },
  {
    name: "search_database",
    description: "Search the product database",
    input_schema: {
      type: "object",
      properties: {
        query: { type: "string" },
        category: { type: "string", enum: ["electronics", "clothing", "food"] },
      },
      required: ["query"],
    },
  },
];
```

### 2. Enviar mensaje con Tools

```ts
const message = await anthropic.messages.create({
  model: "claude-sonnet-4-5-20250514",
  max_tokens: 1024,
  tools,
  messages: [{ role: "user", content: "What's the weather in Tokyo?" }],
});
```

### 3. Manejar la respuesta de Tool Use

```ts
// The model's response contains a tool_use block
if (message.stop_reason === "tool_use") {
  const toolUse = message.content.find((c) => c.type === "tool_use");
  // toolUse = { type: "tool_use", name: "get_weather", input: { city: "Tokyo" } }

  // Execute the function yourself
  const result = await executeFunction(toolUse.name, toolUse.input);

  // Send the result back to the model
  const finalResponse = await anthropic.messages.create({
    model: "claude-sonnet-4-5-20250514",
    max_tokens: 1024,
    tools,
    messages: [
      { role: "user", content: "What's the weather in Tokyo?" },
      { role: "assistant", content: message.content },
      {
        role: "user",
        content: [{
          type: "tool_result",
          tool_use_id: toolUse.id,
          content: JSON.stringify(result),
        }],
      },
    ],
  });
}
```

## Vercel AI SDK (más simple)

```ts
import { streamText, tool } from "ai";
import { anthropic } from "@ai-sdk/anthropic";
import { z } from "zod";

const result = streamText({
  model: anthropic("claude-sonnet-4-5-20250514"),
  tools: {
    getWeather: tool({
      description: "Get current weather for a city",
      parameters: z.object({
        city: z.string().describe("City name"),
      }),
      execute: async ({ city }) => {
        // Your actual implementation
        const weather = await fetchWeather(city);
        return weather;
      },
    }),
  },
  messages,
});
```

El SDK se encarga del loop tool_use -> execute -> tool_result automáticamente.

## Principios clave de diseño

1. **Las buenas descripciones importan** — el LLM las lee para decidir cuándo usar el tool
2. **Restringe los inputs** — usa enums, campos required, descripciones
3. **Maneja los errores** — devuelve mensajes de error como tool results, no hagas throw
4. **No expongas operaciones peligrosas** — el LLM podría llamarlas inesperadamente

## Fuentes

- [Anthropic — Tool Use Guide](https://docs.anthropic.com/en/docs/build-with-claude/tool-use/overview)
- [OpenAI — Function Calling](https://platform.openai.com/docs/guides/function-calling)
- [Vercel AI SDK — Tools](https://sdk.vercel.ai/docs/ai-sdk-core/tools-and-tool-calling)
