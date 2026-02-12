# RAG Pipeline

## Qué es RAG

**Retrieval Augmented Generation** — le das al LLM contexto relevante de tus propios datos antes de que responda.

Sin RAG: el LLM solo sabe lo de su training data (puede alucinar sobre tu empresa)
Con RAG: el LLM responde basándose en tus documentos reales

## Pipeline completo

```
┌─────────────────── INDEXING (one-time) ──────────────────┐
│                                                           │
│  Documents → Extract text → Chunk → Embed → Store in DB  │
│  (PDF, web)   (parsing)    (split)  (vectors)  (pgvector) │
│                                                           │
└───────────────────────────────────────────────────────────┘

┌─────────────────── QUERYING (per request) ────────────────┐
│                                                            │
│  User question → Embed query → Search DB → Top-k chunks   │
│                                              ↓             │
│  LLM ← System prompt + retrieved chunks + user question   │
│   ↓                                                        │
│  Answer (grounded in your documents)                       │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

## Implementación (Next.js + pgvector + Claude)

### 1. Indexing: Ingestar documentos

```ts
import { embed } from "ai";
import { openai } from "@ai-sdk/openai";
import { db } from "@/lib/db";
import { documents } from "@/lib/schema";

async function indexDocument(content: string, source: string) {
  // 1. Chunk the document
  const chunks = chunkByRecursive(content, { size: 1000, overlap: 200 });

  // 2. Embed each chunk
  for (const chunk of chunks) {
    const { embedding } = await embed({
      model: openai.embedding("text-embedding-3-small"),
      value: chunk,
    });

    // 3. Store in pgvector
    await db.insert(documents).values({
      content: chunk,
      source,
      embedding,
    });
  }
}
```

### 2. Retrieval: Encontrar chunks relevantes

```ts
import { cosineDistance, desc, sql } from "drizzle-orm";

async function findRelevantChunks(query: string, topK = 5) {
  // 1. Embed the query
  const { embedding } = await embed({
    model: openai.embedding("text-embedding-3-small"),
    value: query,
  });

  // 2. Search pgvector for similar chunks
  const similarity = sql<number>`1 - (${cosineDistance(documents.embedding, embedding)})`;

  const results = await db
    .select({
      content: documents.content,
      source: documents.source,
      similarity,
    })
    .from(documents)
    .orderBy(desc(similarity))
    .limit(topK);

  return results;
}
```

### 3. Generation: Responder con contexto

```ts
import { streamText } from "ai";
import { anthropic } from "@ai-sdk/anthropic";

export async function POST(req: Request) {
  const { messages } = await req.json();
  const lastMessage = messages[messages.length - 1].content;

  // 1. Retrieve relevant chunks
  const chunks = await findRelevantChunks(lastMessage);

  // 2. Build context
  const context = chunks
    .map((c) => `[Source: ${c.source}]\n${c.content}`)
    .join("\n\n---\n\n");

  // 3. Generate answer with context
  const result = streamText({
    model: anthropic("claude-sonnet-4-5-20250514"),
    system: `You are a helpful assistant. Answer questions based on the provided context.
If the context doesn't contain relevant information, say so.
Always cite which source document you used.

<context>
${context}
</context>`,
    messages,
  });

  return result.toDataStreamResponse();
}
```

## Mejoras de calidad

| Technique | What it does |
|-----------|-------------|
| **Reranking** | Después del retrieval, usa un modelo cross-encoder para re-puntuar resultados y mejorar la precisión |
| **Hybrid search** | Combina búsqueda vectorial (semántica) + búsqueda por keywords (BM25) para lo mejor de ambos mundos |
| **Query expansion** | Reformula la pregunta del usuario para mejorar el retrieval |
| **Metadata filtering** | Filtra por fuente, fecha o categoría antes de la búsqueda vectorial |
| **Citation** | Incluye referencias a las fuentes en la respuesta para que los usuarios puedan verificar |

## Errores comunes

1. **Sin overlap en los chunks** — se pierde contexto en los límites
2. **Chunks demasiado grandes** — retrieval ruidoso, contenido irrelevante mezclado
3. **Chunks demasiado pequeños** — no hay suficiente contexto para una buena respuesta
4. **Modelo de embedding equivocado en la query** — usar un modelo diferente al del indexing
5. **Sin citación** — los usuarios no pueden verificar las respuestas, se pierde confianza

## Fuentes

- [Vercel AI SDK — RAG Guide](https://sdk.vercel.ai/docs/guides/rag-chatbot)
- [Anthropic — RAG Best Practices](https://docs.anthropic.com/en/docs/build-with-claude/retrieval-augmented-generation)
- [Neon — Build a RAG App](https://neon.tech/guides/rag)
- [LangChain — RAG Concepts](https://js.langchain.com/docs/concepts/rag)
