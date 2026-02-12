# Observabilidad

## Por qué importa

Los LLMs son no deterministas. Necesitas saber:
- Qué prompts se están enviando
- Qué responde el modelo
- Cuánto tarda
- Cuánto cuesta
- Cuándo falla o alucina

## Qué registrar

| Field | Why |
|-------|-----|
| Timestamp | Debugging, patrones de uso |
| User ID | Analíticas por usuario, detección de abuso |
| Model used | Seguimiento de costes, comparación de calidad |
| Input tokens | Cálculo de costes |
| Output tokens | Cálculo de costes |
| Latency (ms) | Monitorización de rendimiento |
| Prompt (system + user) | Debugging, mejora de calidad |
| Response | Revisión de calidad, compliance |
| Tool calls | Debugging de agentes |
| Error (if any) | Monitorización de fiabilidad |

## Enfoque simple de logging

```ts
async function trackedGenerate(params: GenerateParams) {
  const start = Date.now();

  try {
    const result = await generateText(params);

    await log({
      model: params.model,
      inputTokens: result.usage.promptTokens,
      outputTokens: result.usage.completionTokens,
      latencyMs: Date.now() - start,
      // Be careful with PII
      promptHash: hash(params.prompt),
      success: true,
    });

    return result;
  } catch (error) {
    await log({
      model: params.model,
      latencyMs: Date.now() - start,
      success: false,
      error: error.message,
    });
    throw error;
  }
}
```

## Plataformas gestionadas

Para producción, usa una plataforma dedicada de observabilidad para LLMs:

| Platform | What it does | Pricing |
|----------|-------------|---------|
| **Langfuse** | Observabilidad open-source para LLMs, trazas, evals | Gratis self-hosted, cloud desde $0 |
| **Helicone** | Logging basado en proxy, caching, rate limiting | Tier gratuito disponible |
| **Braintrust** | Evals + logging | Tier gratuito disponible |

### Integración con Langfuse (Recomendado -- Open Source)

```ts
import { Langfuse } from "langfuse";

const langfuse = new Langfuse({
  publicKey: process.env.LANGFUSE_PUBLIC_KEY,
  secretKey: process.env.LANGFUSE_SECRET_KEY,
});

// Trace a generation
const trace = langfuse.trace({ name: "chat", userId });
const generation = trace.generation({
  name: "llm-call",
  model: "claude-sonnet-4-5-20250514",
  input: messages,
});

const result = await generateText({ model, messages });

generation.end({ output: result.text, usage: result.usage });
```

## Dashboard de métricas clave

Haz seguimiento semanal de esto:
- **Coste total** -- estamos dentro del presupuesto?
- **Coste por conversación** -- está subiendo?
- **Latencia P95** -- los usuarios están esperando demasiado?
- **Tasa de errores** -- está fallando la API?
- **Top queries** -- qué preguntan los usuarios? (esto informa decisiones de producto)

## Fuentes

- [Langfuse Docs](https://langfuse.com/docs)
- [Helicone Docs](https://docs.helicone.ai/)
- [Vercel AI SDK — Telemetry](https://sdk.vercel.ai/docs/ai-sdk-core/telemetry)
- [Braintrust Docs](https://www.braintrust.dev/docs)
