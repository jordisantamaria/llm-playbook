# Provider Abstraction

## El problema

Si hardcodeas un solo proveedor, el coste de cambiar es alto. Los clientes preguntan cosas como: "¿y si OpenAI sube los precios?" o "¿podemos cambiar a Claude?"

## Vercel AI SDK — La solución

Una sola interfaz, cualquier proveedor:

```bash
npm install ai @ai-sdk/anthropic @ai-sdk/openai @ai-sdk/google
```

```ts
import { generateText } from "ai";
import { anthropic } from "@ai-sdk/anthropic";
import { openai } from "@ai-sdk/openai";
import { google } from "@ai-sdk/google";

// Same function, different model — swap in 1 line
const { text } = await generateText({
  model: anthropic("claude-sonnet-4-5-20250514"),
  // model: openai("gpt-4o"),
  // model: google("gemini-2.5-pro"),
  prompt: "Hello",
});
```

## Qué abstrae

| Feature | Works across all providers |
|---------|--------------------------|
| `generateText` | Text generation |
| `streamText` | Streaming |
| `generateObject` | Structured output (Zod) |
| `streamObject` | Streaming structured output |
| `useChat` (React) | Full chat UI hook |
| Tool calling | Define once, works everywhere |

## Cambio de proveedor por entorno

Un patrón muy útil: defines tiers de modelos y cambias entre ellos sin tocar la lógica de negocio:

```ts
// lib/ai.ts
import { anthropic } from "@ai-sdk/anthropic";
import { openai } from "@ai-sdk/openai";

const models = {
  fast: anthropic("claude-haiku-4-5-20241022"),
  balanced: anthropic("claude-sonnet-4-5-20250514"),
  reasoning: openai("o3"),
} as const;

export function getModel(tier: keyof typeof models) {
  return models[tier];
}
```

```ts
// Usage — swap models without changing business logic
const { text } = await generateText({
  model: getModel("balanced"),
  prompt: "...",
});
```

## Valor para consultoría

En la reunión de ventas: "La arquitectura que construyo es provider-agnostic. Si hoy Claude es mejor para tu caso de uso pero mañana necesitas cambiar a GPT o Gemini, es un cambio de configuración, no una reescritura."

Esto es una preocupación real para clientes enterprise japoneses: no quieren vendor lock-in.

## Fuentes

- [Vercel AI SDK - Documentación](https://sdk.vercel.ai/docs)
- [Vercel AI SDK - Providers](https://sdk.vercel.ai/docs/foundations/providers-and-models)
- [Vercel AI SDK - `generateText`](https://sdk.vercel.ai/docs/reference/ai-sdk-core/generate-text)
- [Vercel AI SDK - `streamText`](https://sdk.vercel.ai/docs/reference/ai-sdk-core/stream-text)
