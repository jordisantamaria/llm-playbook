# Optimización de Costes

## Entendiendo los costes de un LLM

Pagas por token. Cada llamada a la API tiene:
- **Input tokens:** system prompt + historial + mensaje del usuario + contexto (chunks de RAG)
- **Output tokens:** respuesta del modelo (normalmente 2-5x más caros que los de input)

## Estimación rápida de costes

```
Monthly cost = (avg_input_tokens × input_price + avg_output_tokens × output_price) × requests_per_month
```

Ejemplo: chatbot de atención al cliente con Claude Sonnet 4.5
- Input medio: 2,000 tokens (system prompt + historial + mensaje del usuario)
- Output medio: 500 tokens
- 10,000 conversaciones/mes

```
Input:  2,000 × $3/1M × 10,000 = $60/month
Output: 500 × $15/1M × 10,000 = $75/month
Total: ~$135/month
```

## Estrategias de optimización

### 1. Model Routing (Mayor impacto)

Usa modelos baratos para tareas simples y los caros para las complejas:

```ts
function selectModel(task: string) {
  // Classification, simple Q&A → cheap model
  if (taskIsSimple(task)) return "claude-haiku-4-5-20241022";  // $0.80/1M input
  // Complex reasoning → expensive model
  return "claude-sonnet-4-5-20250514";  // $3/1M input
}
```

Un modelo barato gestionando el 80% de las peticiones te ahorra ~70% de costes.

### 2. Prompt Caching (Claude)

Claude cachea los prefijos repetidos de los prompts, y pagas un 90% menos por los tokens cacheados:

```ts
const response = await anthropic.messages.create({
  model: "claude-sonnet-4-5-20250514",
  system: [
    {
      type: "text",
      text: longSystemPrompt, // This gets cached after first request
      cache_control: { type: "ephemeral" },
    },
  ],
  messages,
});
```

- Primera petición: precio normal
- Peticiones siguientes (dentro de 5 min): 90% de descuento en la parte cacheada
- Ahorro enorme cuando el system prompt es grande (por ejemplo, contexto de RAG)

### 3. Reducir el tamaño del contexto

- **Recorta el historial de conversación** -- quédate con los últimos N mensajes, no con todo el historial
- **Resume mensajes antiguos** -- usa un modelo barato para comprimir el historial
- **Mejor retrieval en RAG** -- devuelve menos chunks pero más relevantes
- **System prompts más cortos** -- cada token cuesta dinero en cada petición

### 4. Cacheo de respuestas

Cachea las respuestas del LLM para queries idénticas o similares:

```ts
import { Redis } from "@upstash/redis";
const redis = new Redis({ url: process.env.UPSTASH_URL, token: process.env.UPSTASH_TOKEN });

async function cachedGenerate(prompt: string) {
  const cacheKey = `llm:${hashPrompt(prompt)}`;
  const cached = await redis.get(cacheKey);
  if (cached) return cached;

  const result = await generateText({ model, prompt });
  await redis.set(cacheKey, result.text, { ex: 3600 }); // cache 1 hour
  return result.text;
}
```

Ideal para: respuestas de FAQ, queries repetidas, resúmenes de documentos.

### 5. Batching

Procesa múltiples elementos en una sola llamada a la API en vez de uno por uno:

```ts
// Bad: 100 API calls
for (const email of emails) {
  await classify(email);
}

// Good: 1 API call
await classifyBatch(emails); // "Classify each of these emails: [...]"
```

## Fuentes

- [Anthropic — Prompt Caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)
- [Anthropic — Pricing](https://www.anthropic.com/pricing)
- [OpenAI — Pricing](https://openai.com/api/pricing/)
- [Vercel AI SDK — Caching](https://sdk.vercel.ai/docs/ai-sdk-core/settings#experimental_providermetadata)
