# Embeddings

## Qué son los Embeddings

Un embedding es un **vector (array de números)** que representa el significado semántico de un texto. Textos similares tienen vectores similares.

```
"Tokyo is the capital of Japan"  →  [0.023, -0.041, 0.089, ...]  (1536 numbers)
"東京は日本の首都です"            →  [0.021, -0.039, 0.091, ...]  (very similar vector)
"I like pizza"                   →  [0.512, 0.103, -0.287, ...]  (very different vector)
```

## Cómo funciona la similitud

La **cosine similarity** mide lo cerca que están dos vectores (0 = sin relación, 1 = significado idéntico):

```ts
// pgvector does this for you, but conceptually:
cosineSimilarity("Tokyo is the capital of Japan", "東京は日本の首都です") ≈ 0.95
cosineSimilarity("Tokyo is the capital of Japan", "I like pizza") ≈ 0.12
```

## Modelos de Embedding

| Model | Provider | Dimensions | Price (1M tokens) | Notes |
|-------|----------|------------|-------------------|-------|
| text-embedding-3-small | OpenAI | 1536 | $0.02 | Best price/performance |
| text-embedding-3-large | OpenAI | 3072 | $0.13 | Higher accuracy |
| voyage-3 | Voyage AI | 1024 | $0.06 | Recommended by Anthropic |
| embed-v4.0 | Cohere | 1024 | $0.10 | Good multilingual |

**Nota:** Claude/Anthropic no tiene su propio modelo de embedding. Usa OpenAI o Voyage.

## Crear Embeddings

```ts
import OpenAI from "openai";
const openai = new OpenAI();

const response = await openai.embeddings.create({
  model: "text-embedding-3-small",
  input: "Tokyo is the capital of Japan",
});

const vector = response.data[0].embedding; // number[1536]
```

Con el Vercel AI SDK:

```ts
import { embed } from "ai";
import { openai } from "@ai-sdk/openai";

const { embedding } = await embed({
  model: openai.embedding("text-embedding-3-small"),
  value: "Tokyo is the capital of Japan",
});
```

## Puntos clave para producción

- **Usa el mismo modelo para indexar y consultar** — los vectores de modelos diferentes son incompatibles
- **Haz batch embed** cuando proceses muchos documentos (la mayoría de APIs soportan batch requests)
- **El texto en japonés funciona bien** con los modelos de embedding modernos — no necesitas tratamiento especial
- **Las dimensiones importan para el almacenamiento** — 1536 floats x 4 bytes = ~6KB por embedding

## Fuentes

- [OpenAI Embeddings Guide](https://platform.openai.com/docs/guides/embeddings)
- [Voyage AI Docs](https://docs.voyageai.com/)
- [Vercel AI SDK — Embeddings](https://sdk.vercel.ai/docs/ai-sdk-core/embeddings)
- [What are Embeddings? (Vicki Boykis)](https://vickiboykis.com/what_are_embeddings/)
