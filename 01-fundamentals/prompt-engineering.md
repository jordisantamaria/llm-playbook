# Prompt Engineering

## System Prompts

El system prompt define el comportamiento del modelo durante toda la conversación.

```ts
const response = await anthropic.messages.create({
  model: "claude-sonnet-4-5-20250514",
  system: "You are a customer support agent for Acme Corp. Answer only questions about our products. If the question is unrelated, politely redirect.",
  messages: [{ role: "user", content: "..." }],
});
```

**Buenas prácticas:**
- Sé específico con el rol, las restricciones y el formato de output
- Pon las instrucciones más importantes primero
- Usa XML tags para estructurar prompts complejos (Claude los maneja muy bien)

## Few-Shot Prompting

Proporciona ejemplos del patrón input/output que quieres:

```
Extract the company name and amount from this invoice.

Example 1:
Input: "Invoice from Toyota Motor Corp. Total: ¥1,500,000"
Output: {"company": "Toyota Motor Corp.", "amount": 1500000, "currency": "JPY"}

Example 2:
Input: "Bill to: Sony Group. Amount due: ¥850,000"
Output: {"company": "Sony Group", "amount": 850000, "currency": "JPY"}

Now extract from:
Input: "Payment request from Rakuten Inc. for ¥2,300,000"
```

## Chain-of-Thought (CoT)

Pídele al modelo que piense paso a paso antes de responder:

```
Analyze this customer complaint and recommend an action.
Think step by step:
1. What is the customer's core issue?
2. What is the severity level?
3. What department should handle this?
4. What is the recommended response?
```

- Mejora la precisión en tareas de razonamiento complejo
- Útil para: clasificación, análisis, decisiones de varios pasos
- Claude soporta "extended thinking" de forma nativa -- razonamiento dedicado antes de responder

## Patrones de Prompting para Producción

| Pattern | When to use |
|---------|-------------|
| **Role assignment** | Siempre -- define los límites de comportamiento |
| **Output format specification** | Cuando necesitas datos estructurados (JSON, markdown) |
| **Few-shot examples** | Cuando la tarea es ambigua o muy específica del dominio |
| **Chain-of-thought** | Razonamiento complejo, análisis, tareas de varios pasos |
| **Constraints** | "Do NOT answer questions about...", "Always respond in Japanese" |
| **XML tags** | Prompts complejos con Claude -- `<context>`, `<instructions>`, `<examples>` |

## Fuentes

- [Anthropic - Prompt Engineering Guide](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)
- [OpenAI - Prompt Engineering Best Practices](https://platform.openai.com/docs/guides/prompt-engineering)
- [Anthropic - Extended Thinking](https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking)
- [Learn Prompting](https://learnprompting.org/)
