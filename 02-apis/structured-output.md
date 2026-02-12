# Structured Output

## El problema

Los LLMs devuelven texto libre. En producción, necesitas **JSON fiable** que puedas parsear y guardar.

```
// Quieres esto, no un muro de texto:
{ "company": "Toyota", "amount": 1500000, "currency": "JPY" }
```

## Vercel AI SDK + Zod (Recomendado)

El enfoque más limpio: defines un schema con Zod y obtienes output tipado:

```ts
import { generateObject } from "ai";
import { anthropic } from "@ai-sdk/anthropic";
import { z } from "zod";

const { object } = await generateObject({
  model: anthropic("claude-sonnet-4-5-20250514"),
  schema: z.object({
    company: z.string(),
    amount: z.number(),
    currency: z.enum(["JPY", "USD", "EUR"]),
    date: z.string().optional(),
  }),
  prompt: "Extract invoice data: 'Invoice from Toyota Motor Corp. Total: ¥1,500,000 dated 2024-03-15'",
});

// object is fully typed: { company: string, amount: number, currency: "JPY" | "USD" | "EUR", date?: string }
```

## Claude API (Nativa)

Puedes forzar structured output en Claude usando `tool_choice` para que el modelo devuelva datos con un schema exacto:

```ts
const message = await anthropic.messages.create({
  model: "claude-sonnet-4-5-20250514",
  max_tokens: 1024,
  messages: [{ role: "user", content: "Extract invoice data..." }],
  // Use tool_choice to force structured output
  tools: [{
    name: "extract_invoice",
    description: "Extract structured data from an invoice",
    input_schema: {
      type: "object",
      properties: {
        company: { type: "string" },
        amount: { type: "number" },
        currency: { type: "string", enum: ["JPY", "USD", "EUR"] },
      },
      required: ["company", "amount", "currency"],
    },
  }],
  tool_choice: { type: "tool", name: "extract_invoice" },
});

const data = message.content[0].input; // structured JSON
```

## OpenAI API (Nativa)

OpenAI tiene su propio mecanismo con `response_format` y `json_schema`:

```ts
const completion = await openai.chat.completions.create({
  model: "gpt-4o",
  messages: [{ role: "user", content: "Extract invoice data..." }],
  response_format: {
    type: "json_schema",
    json_schema: {
      name: "invoice",
      schema: {
        type: "object",
        properties: {
          company: { type: "string" },
          amount: { type: "number" },
        },
        required: ["company", "amount"],
      },
    },
  },
});

const data = JSON.parse(completion.choices[0].message.content);
```

## Cuándo usar cada uno

| Approach | Pros | Cons |
|----------|------|------|
| **Vercel AI SDK + Zod** | Type-safe, provider-agnostic, cleanest DX | Extra dependency |
| **Claude tool_choice** | No extra deps, forces exact schema | Claude-specific |
| **OpenAI json_schema** | Native, reliable | OpenAI-specific |

## Punto clave para consultoría

Structured output es lo que hace que los LLMs sean útiles en producción. Sin esto, no puedes de forma fiable:
- Guardar resultados del LLM en una base de datos
- Usar el output del LLM en lógica downstream
- Construir pipelines fiables

Esto es un diferenciador clave cuando hablas con clientes: "la IA no solo chatea, genera datos estructurados que tus sistemas pueden consumir directamente."

## Fuentes

- [Vercel AI SDK - `generateObject`](https://sdk.vercel.ai/docs/reference/ai-sdk-core/generate-object)
- [Claude - Tool use (structured output)](https://docs.anthropic.com/en/docs/build-with-claude/tool-use)
- [OpenAI - Structured Outputs](https://platform.openai.com/docs/guides/structured-outputs)
- [Zod - Documentación](https://zod.dev/)
