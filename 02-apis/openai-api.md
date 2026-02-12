# OpenAI API

## Setup

```bash
npm install openai
```

```ts
import OpenAI from "openai";

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
});
```

## Chat Completions (Core)

La API principal de OpenAI. Fíjate que aquí el system prompt va como un mensaje más dentro del array, no como parámetro separado:

```ts
const completion = await openai.chat.completions.create({
  model: "gpt-4o",
  messages: [
    { role: "system", content: "You are a helpful assistant." },
    { role: "user", content: "Explain RAG in one paragraph" },
  ],
});

console.log(completion.choices[0].message.content);
```

## Streaming

Para streaming, simplemente añades `stream: true` a la llamada:

```ts
const stream = await openai.chat.completions.create({
  model: "gpt-4o",
  messages: [{ role: "user", content: "Hello" }],
  stream: true,
});

for await (const chunk of stream) {
  const content = chunk.choices[0]?.delta?.content;
  if (content) process.stdout.write(content);
}
```

## Models & Pricing

| Model | Input (per 1M tokens) | Output (per 1M tokens) | Best for |
|-------|----------------------|------------------------|----------|
| GPT-4o | $2.50 | $10 | General purpose, good balance |
| GPT-4o-mini | $0.15 | $0.60 | Fast/cheap tasks, classification |
| o3 | $10 | $40 | Complex reasoning |

## Embeddings

OpenAI tiene modelos de embeddings nativos, algo que Claude no ofrece. Los usas para convertir texto en vectores numéricos (esencial para búsqueda semántica y RAG):

```ts
const embedding = await openai.embeddings.create({
  model: "text-embedding-3-small",
  input: "The quick brown fox",
});

const vector = embedding.data[0].embedding; // number[] — 1536 dimensions
```

| Embedding Model | Dimensions | Price (per 1M tokens) |
|----------------|------------|----------------------|
| text-embedding-3-small | 1536 | $0.02 |
| text-embedding-3-large | 3072 | $0.13 |

## Diferencias clave vs Claude

- El system prompt va como un mensaje con `role: "system"`, no como parámetro de primer nivel
- La respuesta viene en `choices[0].message.content` (no es un array)
- Tiene modelos de embeddings nativos (Claude no tiene; usa OpenAI o Voyage para embeddings)
- La sintaxis de function calling difiere un poco (ver structured-output.md)

## Fuentes

- [Documentación oficial de la API de OpenAI](https://platform.openai.com/docs/api-reference/chat)
- [Guía de streaming de OpenAI](https://platform.openai.com/docs/api-reference/streaming)
- [Guía de embeddings](https://platform.openai.com/docs/guides/embeddings)
- [Precios de OpenAI](https://openai.com/api/pricing/)
