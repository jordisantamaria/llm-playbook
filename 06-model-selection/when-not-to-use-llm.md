# Cuándo NO usar un LLM

## La pregunta clave

"Se puede resolver con reglas deterministas o código tradicional?"
Si la respuesta es sí, no uses un LLM.

Los LLMs son caros, lentos y no deterministas. Úsalos solo cuando necesites **comprensión del lenguaje**.

## No uses un LLM para

| Task | Better Alternative |
|------|--------------------|
| **Búsqueda por palabras clave** | Full-text search (PostgreSQL tsvector, Elasticsearch) |
| **Validación de datos** | Zod schemas, regex, reglas de negocio |
| **Clasificación simple** (3-5 categorías con reglas claras) | If/else, decision tree, regex |
| **Rellenar templates** | String interpolation |
| **Cálculos** | Código |
| **Obtener datos** | SQL queries, API calls |
| **Ordenar/filtrar** | Database queries |
| **Coincidencia exacta** | WHERE clause |

## Sí usa un LLM para

| Task | Why LLM? |
|------|----------|
| **Entender la intención** | "Cancela mi pedido" vs "Cuál es la política de cancelación?" |
| **Resumen** | Condensar 10 páginas en 3 puntos |
| **Extracción de texto no estructurado** | Sacar datos de emails, PDFs, texto libre |
| **Traducción/localización** | Traducción con contexto |
| **Búsqueda semántica** | Encontrar contenido relevante por significado, no por palabras clave |
| **Generación** | Redactar emails, informes, respuestas |
| **Clasificación compleja** | Sentimiento, tono, muchas categorías, inputs ambiguos |

## Enfoque híbrido (Best Practice)

```
User: "Show me orders from last week over ¥100,000"
  ↓
LLM extracts intent + parameters:
  { action: "search_orders", dateRange: "last_week", minAmount: 100000 }
  ↓
Traditional code executes the query:
  SELECT * FROM orders WHERE created_at > NOW() - INTERVAL '7 days' AND amount > 100000
  ↓
LLM formats the response:
  "You had 3 orders last week over ¥100,000: ..."
```

El LLM se encarga del lenguaje. La base de datos se encarga de los datos. Cada uno hace lo que mejor sabe hacer.

## Ángulo de consultoría

Este conocimiento es crucial para reuniones con clientes. Muchos clientes piensan que "la IA lo puede hacer todo." Tú aportas valor diciendo: "Esta parte no necesita IA -- una simple query a la base de datos es más rápida, más barata y más fiable. Usamos IA aquí, donde realmente necesitas comprensión del lenguaje."

Esto le ahorra dinero al cliente y genera confianza.

## Fuentes

- [Anthropic — When to Use Claude](https://docs.anthropic.com/en/docs/about-claude/use-case-guides)
- [Eugene Yan — When NOT to Use LLMs](https://eugeneyan.com/writing/llm-patterns/)
