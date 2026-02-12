# Vector Databases

## Qué hacen

Almacenan embeddings y te permiten buscar los vectores "más similares" de forma eficiente.

```
Query: "What is the refund policy?"
  → Create embedding of query
  → Search vector DB for closest matches
  → Return top-k most similar document chunks
```

## Comparación de opciones

### pgvector (PostgreSQL Extension)

**Ideal para: equipos que ya usan PostgreSQL (tu caso con Neon)**

```sql
-- Enable extension
CREATE EXTENSION vector;

-- Create table with vector column
CREATE TABLE documents (
  id SERIAL PRIMARY KEY,
  content TEXT,
  embedding VECTOR(1536)  -- matches your embedding model dimensions
);

-- Insert
INSERT INTO documents (content, embedding)
VALUES ('Refund policy: 30 days...', '[0.023, -0.041, ...]');

-- Search (cosine similarity)
SELECT content, 1 - (embedding <=> $1) AS similarity
FROM documents
ORDER BY embedding <=> $1
LIMIT 5;
```

Pros:
- Sin servicio extra — corre en tu Postgres existente (Neon lo soporta)
- SQL que ya conoces
- Transacciones, joins y filtrado con SQL normal
- Gratis (sin coste adicional más allá de Neon)

Contras:
- No es tan rápido como las vector DBs dedicadas a escala masiva (millones de vectores)
- Para la mayoría de apps, esto no importa

### Pinecone

**Ideal para: búsqueda vectorial a gran escala como servicio gestionado**

```ts
import { Pinecone } from "@pinecone-database/pinecone";

const pc = new Pinecone({ apiKey: process.env.PINECONE_API_KEY });
const index = pc.index("documents");

// Upsert
await index.upsert([{
  id: "doc-1",
  values: embedding,  // number[]
  metadata: { source: "policy.pdf", page: 3 },
}]);

// Query
const results = await index.query({
  vector: queryEmbedding,
  topK: 5,
  includeMetadata: true,
});
```

Pros:
- Servicio gestionado, escala automáticamente
- Rápido a muy gran escala
- Buen filtrado por metadata

Contras:
- Un servicio más que gestionar
- Cuesta dinero (el free tier es limitado)
- Los datos viven fuera de tu DB principal

### ChromaDB

**Ideal para: prototipado local y experimentos**

- Corre en local, sin setup necesario
- Bueno para probar RAG antes de invertir en infraestructura
- No recomendado para producción

## Framework de decisión

```
Do you already use PostgreSQL? → pgvector
  ↓ No
Need to search millions of vectors? → Pinecone
  ↓ No
Just prototyping? → ChromaDB
```

**Para tu stack (Neon + Drizzle):** pgvector es la elección obvia. Cero infraestructura adicional.

## pgvector con Neon + Drizzle

```ts
// schema.ts
import { pgTable, serial, text, vector } from "drizzle-orm/pg-core";

export const documents = pgTable("documents", {
  id: serial("id").primaryKey(),
  content: text("content").notNull(),
  embedding: vector("embedding", { dimensions: 1536 }),
});
```

## Fuentes

- [pgvector GitHub](https://github.com/pgvector/pgvector)
- [Neon — pgvector Guide](https://neon.tech/docs/extensions/pgvector)
- [Pinecone Docs](https://docs.pinecone.io/)
- [Supabase — pgvector Guide](https://supabase.com/docs/guides/ai/vector-columns)
- [Drizzle ORM — pgvector](https://orm.drizzle.team/docs/extensions/pg#pg_vector)
