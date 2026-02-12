# Cost Estimation (コスト見積もり)

## Cómo Estimar Costes de API para un Cliente

La pregunta que siempre hacen: "¿Cuánto va a costar al mes?"

## Fórmula Base

```
Coste mensual = (tokens_input × precio_input + tokens_output × precio_output) × requests/mes
```

## Estimaciones Rápidas por Caso de Uso

### Chatbot de Soporte (Claude Sonnet 4.5)

| Parámetro | Valor típico |
|-----------|-------------|
| System prompt | ~500 tokens |
| Historial (últimos 10 mensajes) | ~2,000 tokens |
| Mensaje del usuario | ~100 tokens |
| Contexto RAG (5 chunks) | ~2,500 tokens |
| **Total input** | **~5,100 tokens/request** |
| Respuesta del modelo | ~500 tokens |
| **Total output** | **~500 tokens/request** |

Para 5,000 conversaciones/mes:
```
Input:  5,100 × $3/1M × 5,000 = $76.50
Output: 500 × $15/1M × 5,000  = $37.50
Total: ~$114/mes ≈ ¥17,000/mes
```

### Extracción de Documentos (Claude Haiku 4.5)

| Parámetro | Valor típico |
|-----------|-------------|
| Imagen de documento | ~1,500 tokens |
| Prompt de extracción | ~200 tokens |
| **Total input** | **~1,700 tokens/request** |
| Structured output | ~300 tokens |
| **Total output** | **~300 tokens/request** |

Para 2,000 documentos/mes:
```
Input:  1,700 × $0.80/1M × 2,000 = $2.72
Output: 300 × $4/1M × 2,000     = $2.40
Total: ~$5/mes ≈ ¥750/mes
```

### RAG Indexación (OpenAI Embeddings)

| Parámetro | Valor típico |
|-----------|-------------|
| 500 documentos × 10 páginas × 4 chunks/página | 20,000 chunks |
| ~500 tokens/chunk | 10M tokens total |

```
Indexación inicial: 10M × $0.02/1M = $0.20 (una sola vez)
Re-indexación mensual (10% de docs cambian): ~$0.02/mes
```

## Tabla de Referencia Rápida

| Caso de uso | Modelo | Volumen | Coste/mes |
|-------------|--------|---------|-----------|
| Chatbot soporte | Sonnet 4.5 | 5K conv | ~¥17,000 |
| Chatbot soporte | Con routing (70% Haiku) | 5K conv | ~¥6,000 |
| Extracción docs | Haiku 4.5 | 2K docs | ~¥750 |
| Knowledge base | Sonnet 4.5 | 2K queries | ~¥5,000 |
| Clasificación emails | Haiku 4.5 | 10K emails | ~¥1,500 |

## Costes de Infraestructura (Además de API)

| Servicio | Coste/mes | Notas |
|---------|-----------|-------|
| Neon PostgreSQL (Pro) | $19 | Incluye pgvector |
| Vercel (Pro) | $20 | Deploy de la app |
| Upstash Redis | $0-10 | Cache de respuestas |
| **Total infra** | **~$40-50/mes** | |

## Cómo Presentar al Cliente

No digas solo "¥17,000/mes". Compáralo con el coste humano:

```
Coste AI: ¥17,000/mes
Coste de 1 persona de soporte: ¥300,000/mes
Si la AI maneja 60% de queries: ahorro de ¥180,000/mes
ROI: ¥180,000 - ¥17,000 = ¥163,000/mes de ahorro neto
```

## Fuentes

- [Anthropic — Pricing](https://www.anthropic.com/pricing)
- [OpenAI — Pricing](https://openai.com/api/pricing/)
- [Anthropic — Token Counting](https://docs.anthropic.com/en/docs/build-with-claude/token-counting)
