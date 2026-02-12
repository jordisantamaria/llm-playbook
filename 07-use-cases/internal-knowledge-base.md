# Internal Knowledge Base (社内ナレッジベース)

## El Caso de Uso Más Demandado en Japón

Todas las empresas japonesas tienen el mismo problema: documentos internos dispersos en Confluence, SharePoint, Google Drive, wikis internas, PDFs en carpetas compartidas. Los empleados pierden horas buscando información.

**Propuesta:** Un chatbot que entiende y responde preguntas sobre la documentación interna de la empresa.

## Dolor del Cliente

- "新入社員が情報を見つけるのに時間がかかる" (los nuevos empleados tardan mucho en encontrar información)
- Manuales desactualizados y dispersos
- El conocimiento está en la cabeza de personas senior que se van jubilando
- El buscador de Confluence/SharePoint no encuentra lo que necesitan

## Arquitectura

```
Fuentes de datos                  Pipeline de indexación
┌──────────────┐
│ Confluence   │──┐
│ SharePoint   │──┤   Extraer texto → Chunking → Embeddings → pgvector
│ Google Drive │──┤
│ PDFs locales │──┘
└──────────────┘

Consulta del usuario
┌──────────────────────────────────────────┐
│ "¿Cuál es el proceso de aprobación de    │
│  gastos de más de 10万円?"               │
│                                          │
│  → Embed query → Buscar en pgvector      │
│  → Top 5 chunks relevantes              │
│  → Enviar a Claude con contexto          │
│  → Respuesta + citación de fuente        │
└──────────────────────────────────────────┘
```

## Features Que Impresionan en la 商談

1. **Citación de fuentes** — "Según el Manual de Gastos (p.12), el límite es..."
2. **Soporte japonés nativo** — funciona con documentos en JP sin configuración extra
3. **Control de acceso** — solo muestra información que el usuario tiene permiso de ver
4. **Actualización incremental** — cuando se actualiza un documento, se re-indexa solo ese
5. **Feedback loop** — thumbs up/down en las respuestas para mejorar la calidad

## Stack Sugerido

- **Frontend:** Next.js + Vercel AI SDK (streaming chat)
- **LLM:** Claude Sonnet 4.5 (buen balance calidad/coste)
- **Embeddings:** OpenAI text-embedding-3-small
- **Vector DB:** pgvector en Neon
- **Ingesta:** Unstructured.io o LlamaIndex para parsear PDFs/Office docs
- **Deploy:** Vercel (o infraestructura del cliente)

## Estimación de Costes para el Cliente

Para una empresa con 500 documentos internos, 100 usuarios, ~2000 consultas/mes:

| Componente | Coste mensual |
|-----------|--------------|
| Claude Sonnet (2K input + 500 output × 2000) | ~$27 |
| Embeddings (re-indexación ocasional) | ~$2 |
| Neon PostgreSQL | $19 (Pro plan) |
| Vercel | $20 |
| **Total** | **~$68/mes** |

Comparar con: tiempo ahorrado por empleado × número de empleados. Si 100 personas ahorran 30 min/semana = 200 horas/mes. A ¥3,000/hora = **¥600,000/mes de ahorro**.

## Preguntas para la 商談

- "¿Dónde están almacenados sus documentos internos actualmente?"
- "¿Cuántos documentos tienen aproximadamente?"
- "¿En qué idioma(s) están?"
- "¿Tienen control de acceso? ¿Hay documentos que solo ciertos empleados pueden ver?"
- "¿Con qué frecuencia se actualizan los documentos?"
- "¿Cuál es el mayor problema que tienen los empleados para encontrar información?"

## Fuentes

- [Anthropic — RAG Best Practices](https://docs.anthropic.com/en/docs/build-with-claude/retrieval-augmented-generation)
- [Neon — Build a RAG App](https://neon.tech/guides/rag)
- [Unstructured.io — Document Parsing](https://docs.unstructured.io/)
- [LlamaIndex — Document Loaders](https://docs.llamaindex.ai/en/stable/module_guides/loading/)
