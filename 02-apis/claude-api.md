# Claude API

## Setup

```bash
npm install @anthropic-ai/sdk
```

```ts
import Anthropic from "@anthropic-ai/sdk";

const anthropic = new Anthropic({
  apiKey: process.env.ANTHROPIC_API_KEY,
});
```

## Messages API (Core)

La API principal de Claude se basa en mensajes. Le pasas un modelo, un system prompt y un array de mensajes:

```ts
const message = await anthropic.messages.create({
  model: "claude-sonnet-4-5-20250514",
  max_tokens: 1024,
  system: "You are a helpful assistant.",
  messages: [
    { role: "user", content: "Explain RAG in one paragraph" },
  ],
});

console.log(message.content[0].text);
```

## Streaming

Para respuestas en tiempo real (ideal para UIs tipo chat), usa streaming:

```ts
const stream = anthropic.messages.stream({
  model: "claude-sonnet-4-5-20250514",
  max_tokens: 1024,
  messages: [{ role: "user", content: "Hello" }],
});

for await (const event of stream) {
  if (event.type === "content_block_delta" && event.delta.type === "text_delta") {
    process.stdout.write(event.delta.text);
  }
}
```

## Models & Pricing

| Model | Input (per 1M tokens) | Output (per 1M tokens) | Best for |
|-------|----------------------|------------------------|----------|
| Opus 4 | $15 | $75 | Complex reasoning, coding |
| Sonnet 4.5 | $3 | $15 | Best balance of speed/quality |
| Haiku 4.5 | $0.80 | $4 | Fast tasks, classification, routing |

## Multi-modal (Vision)

Claude puede procesar imágenes directamente. Le pasas la imagen en base64 junto con tu prompt de texto:

```ts
const message = await anthropic.messages.create({
  model: "claude-sonnet-4-5-20250514",
  max_tokens: 1024,
  messages: [{
    role: "user",
    content: [
      {
        type: "image",
        source: { type: "base64", media_type: "image/png", data: base64Image },
      },
      { type: "text", text: "Extract all text from this invoice" },
    ],
  }],
});
```

## Extended Thinking

Extended thinking le permite al modelo razonar internamente antes de darte la respuesta final:

```ts
const message = await anthropic.messages.create({
  model: "claude-sonnet-4-5-20250514",
  max_tokens: 16000,
  thinking: {
    type: "enabled",
    budget_tokens: 10000,
  },
  messages: [{ role: "user", content: "Analyze this complex problem..." }],
});
```

- El modelo razona internamente antes de responder
- Funciona bien para análisis complejos, matemáticas y razonamiento multi-paso
- También pagas por los thinking tokens, así que úsalo con cabeza

## Diferencias clave vs OpenAI

- Claude usa `system` como parámetro de primer nivel, no como un rol de mensaje
- Claude devuelve `content` como un array (puede contener text + tool use)
- Claude entiende tags XML de forma nativa, lo cual es muy útil para prompts estructurados
- Extended thinking es exclusivo de Claude

## Fuentes

- [Documentación oficial de la API de Claude](https://docs.anthropic.com/en/api/messages)
- [Guía de streaming de Claude](https://docs.anthropic.com/en/api/streaming)
- [Extended thinking](https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking)
- [Vision (multi-modal)](https://docs.anthropic.com/en/docs/build-with-claude/vision)
- [Precios de Anthropic](https://www.anthropic.com/pricing)
