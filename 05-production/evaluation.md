# Evaluación

## El problema

Cómo sabes si el LLM está dando buenas respuestas? No puedes hacer unit tests con output no determinista.

## Enfoques de evaluación

### 1. LLM-as-Judge

Usa un LLM (más potente) para evaluar el output de otro LLM:

```ts
const evaluation = await generateObject({
  model: anthropic("claude-sonnet-4-5-20250514"),
  schema: z.object({
    relevance: z.number().min(1).max(5).describe("How relevant is the answer to the question?"),
    accuracy: z.number().min(1).max(5).describe("How factually accurate is the answer?"),
    completeness: z.number().min(1).max(5).describe("Does the answer fully address the question?"),
    reasoning: z.string().describe("Brief explanation of scores"),
  }),
  prompt: `Evaluate this Q&A pair:
Question: ${question}
Context provided: ${context}
Answer: ${answer}

Score each dimension from 1-5.`,
});
```

- Barato y escalable
- No es perfecto, pero detecta fallos obvios
- Bueno para regression testing (ejecútalo sobre un test set después de cada cambio)

### 2. Ground Truth Comparison

Mantén un conjunto de pares pregunta-respuesta:

```ts
const testCases = [
  {
    question: "What is the refund policy?",
    expectedAnswer: "30-day money-back guarantee",
    context: "Our refund policy allows returns within 30 days...",
  },
  // ... more test cases
];

for (const testCase of testCases) {
  const answer = await rag.query(testCase.question);
  const score = await evaluateWithLLM(testCase.question, testCase.expectedAnswer, answer);
  console.log(`${testCase.question}: ${score}/5`);
}
```

### 3. Retrieval Evaluation (para RAG)

Testea que se están recuperando los chunks correctos:

```ts
const retrievalTests = [
  {
    query: "refund policy",
    expectedSources: ["terms-of-service.pdf"],
  },
];

for (const test of retrievalTests) {
  const chunks = await findRelevantChunks(test.query);
  const sources = chunks.map((c) => c.source);
  const recall = test.expectedSources.filter((s) => sources.includes(s)).length / test.expectedSources.length;
  console.log(`Retrieval recall: ${recall}`);
}
```

### 4. Evaluación humana

Para aplicaciones de alto riesgo:
- Muestrea N conversaciones por semana
- Haz que humanos evalúen la calidad
- Sigue las tendencias a lo largo del tiempo
- Es lo más preciso pero no escala

## Qué medir

| Metric | What it measures | How |
|--------|-----------------|-----|
| **Relevance** | Responde a la pregunta? | LLM-as-judge |
| **Faithfulness** | Está basada en el contexto proporcionado? (sin alucinaciones) | LLM-as-judge |
| **Retrieval recall** | Se están recuperando los documentos correctos? | Ground truth |
| **Latency** | Tiempo de respuesta | Automatizado |
| **User satisfaction** | Thumbs up/down | Feedback in-app |

## Setup práctico

1. **Crea un test set** -- 50-100 preguntas representativas con respuestas esperadas
2. **Ejecuta evals en cada cambio** -- nuevo prompt, nuevo modelo, nueva estrategia de chunking
3. **Haz seguimiento de los scores a lo largo del tiempo** -- detecta regresiones antes que los usuarios
4. **A/B test en producción** -- compara versiones de modelos con tráfico real

## Fuentes

- [Anthropic — Evaluating AI Models](https://docs.anthropic.com/en/docs/build-with-claude/develop-tests)
- [Langfuse — LLM Evaluation](https://langfuse.com/docs/scores/overview)
- [RAGAS — RAG Evaluation Framework](https://docs.ragas.io/)
- [Braintrust — Evals](https://www.braintrust.dev/docs/guides/evals)
