# Search Enhancement (検索の強化)

## El Caso de Uso

Mejorar el buscador existente de una aplicación con búsqueda semántica. En vez de buscar por keywords exactas, buscar por significado.

## El Problema

Búsqueda keyword (tradicional):
```
Query: "devolver producto"
Resultados: solo documentos que contienen las palabras "devolver" Y "producto"
No encuentra: "política de reembolso", "return policy", "返品について"
```

Búsqueda semántica (con embeddings):
```
Query: "devolver producto"
Resultados: "política de reembolso", "return policy", "返品について", "proceso de devolución"
Encuentra documentos por SIGNIFICADO, no por palabras exactas
```

## Implementación: Hybrid Search

La mejor práctica es combinar ambos tipos:

```sql
-- pgvector: búsqueda semántica + keyword en una sola query
WITH semantic AS (
  SELECT id, content, 1 - (embedding <=> $1) AS semantic_score
  FROM documents
  ORDER BY embedding <=> $1
  LIMIT 20
),
keyword AS (
  SELECT id, content, ts_rank(to_tsvector('japanese', content), plainto_tsquery('japanese', $2)) AS keyword_score
  FROM documents
  WHERE to_tsvector('japanese', content) @@ plainto_tsquery('japanese', $2)
  LIMIT 20
)
SELECT
  COALESCE(s.id, k.id) AS id,
  COALESCE(s.content, k.content) AS content,
  (COALESCE(s.semantic_score, 0) * 0.7 + COALESCE(k.keyword_score, 0) * 0.3) AS combined_score
FROM semantic s
FULL OUTER JOIN keyword k ON s.id = k.id
ORDER BY combined_score DESC
LIMIT 10;
```

## Cuándo Proponerlo al Cliente

- "Nuestros usuarios se quejan de que el buscador no encuentra lo que buscan"
- "Tenemos contenido en japonés e inglés y el buscador no los conecta"
- "Los usuarios no saben los términos exactos para buscar"

## Ventajas sobre Elasticsearch/Algolia

| | Keyword (Algolia/ES) | Semántico (pgvector) | Hybrid |
|--|---------------------|---------------------|--------|
| Búsqueda exacta | Excelente | Mediocre | Excelente |
| Búsqueda por significado | No | Excelente | Excelente |
| Multi-idioma | Necesita configuración | Nativo | Nativo |
| Coste adicional | Algolia/ES pricing | Ya incluido en Postgres | Ya incluido |

## Fuentes

- [Neon — Semantic Search with pgvector](https://neon.tech/docs/extensions/pgvector)
- [Supabase — Hybrid Search](https://supabase.com/docs/guides/ai/hybrid-search)
- [pgvector GitHub](https://github.com/pgvector/pgvector)
- [PostgreSQL Full Text Search](https://www.postgresql.org/docs/current/textsearch.html)
