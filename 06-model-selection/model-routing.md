# Model Routing

## El concepto

No todas las peticiones necesitan tu modelo más caro. Enruta las peticiones al tier de modelo apropiado:

```
User request
    ↓
Classifier (cheap model)
    ↓
┌───────────────────────────────┐
│ Simple? → Haiku ($0.80/1M)    │  "What are your hours?" → FAQ lookup
│ Medium? → Sonnet ($3/1M)      │  "Explain my billing" → needs reasoning
│ Complex? → Opus ($15/1M)      │  "Analyze this contract" → deep analysis
└───────────────────────────────┘
```

## Implementación

```ts
import { generateObject, generateText } from "ai";
import { anthropic } from "@ai-sdk/anthropic";
import { z } from "zod";

// Step 1: Classify with cheap model
const { object: classification } = await generateObject({
  model: anthropic("claude-haiku-4-5-20241022"),
  schema: z.object({
    complexity: z.enum(["simple", "medium", "complex"]),
    reasoning: z.string(),
  }),
  prompt: `Classify this user request complexity:
- simple: factual lookup, FAQ, yes/no questions
- medium: requires some reasoning or explanation
- complex: multi-step analysis, document review, nuanced advice

Request: "${userMessage}"`,
});

// Step 2: Route to appropriate model
const models = {
  simple: anthropic("claude-haiku-4-5-20241022"),
  medium: anthropic("claude-sonnet-4-5-20250514"),
  complex: anthropic("claude-opus-4-20250514"),
};

const { text } = await generateText({
  model: models[classification.complexity],
  messages,
});
```

## Impacto en costes

Distribución típica (chatbot de atención al cliente):
- 60% queries simples -> Haiku: 60% x $0.80 = $0.48 efectivo
- 30% queries medias -> Sonnet: 30% x $3 = $0.90 efectivo
- 10% queries complejas -> Opus: 10% x $15 = $1.50 efectivo
- **Coste combinado: ~$2.88/1M** vs $15/1M si usas Opus para todo = **80% de ahorro**

## Alternativa: Cascading

Empieza barato, escala si la respuesta es incierta:

```ts
// Try cheap model first
const { object: result } = await generateObject({
  model: anthropic("claude-haiku-4-5-20241022"),
  schema: z.object({
    answer: z.string(),
    confidence: z.number().min(0).max(1),
  }),
  prompt: userMessage,
});

// If uncertain, escalate
if (result.confidence < 0.7) {
  const better = await generateText({
    model: anthropic("claude-sonnet-4-5-20250514"),
    prompt: userMessage,
  });
  return better.text;
}

return result.answer;
```

## Fuentes

- [Anthropic — Model Selection](https://docs.anthropic.com/en/docs/about-claude/models)
- [Martian — LLM Router](https://withmartian.com/blog/router)
- [Not Diamond — Model Routing](https://www.notdiamond.ai/)
