# Customer Support (カスタマーサポート)

## El Caso de Uso

Chatbot que responde preguntas frecuentes de clientes y escala a un agente humano cuando no puede resolver.

## Dolor del Cliente

- Equipo de soporte saturado con preguntas repetitivas
- Horario limitado (no 24/7)
- Tiempo de respuesta lento
- Coste alto por agente humano

## Arquitectura

```
Cliente envía mensaje
        ↓
┌─ Clasificador (Haiku) ────────────────────────┐
│                                                │
│  FAQ simple? → RAG sobre base de conocimiento  │
│  Consulta de cuenta? → Tool use (buscar DB)    │
│  Queja/complejo? → Escalar a humano            │
│  Fuera de scope? → "No puedo ayudar con eso"   │
└────────────────────────────────────────────────┘
```

## Features Clave

### 1. Clasificación + Routing

```ts
const { object } = await generateObject({
  model: anthropic("claude-haiku-4-5-20241022"),
  schema: z.object({
    category: z.enum(["faq", "account_query", "complaint", "out_of_scope"]),
    confidence: z.number(),
    language: z.enum(["ja", "en"]),
  }),
  prompt: `Clasifica este mensaje de soporte: "${userMessage}"`,
});
```

### 2. Escalado a Humano

Criterios para escalar:
- Confianza del clasificador < 0.7
- El usuario pide hablar con una persona
- Queja o tono negativo
- Después de 3 respuestas insatisfactorias (detectadas por feedback)

### 3. Multi-idioma

Claude maneja japonés e inglés nativamente. Un solo bot para ambos sin configuración extra.

## Métricas para el Cliente

| Métrica | Sin AI | Con AI |
|---------|--------|--------|
| Tiempo de primera respuesta | 2-4 horas | Instantáneo |
| Resolución sin humano | 0% | 60-70% |
| Coste por consulta | ¥500-1000 | ¥5-20 |
| Disponibilidad | 9:00-18:00 | 24/7 |

## Fuentes

- [Anthropic — Customer Support Use Case](https://docs.anthropic.com/en/docs/about-claude/use-case-guides/customer-support-agent)
- [Intercom — AI Customer Service](https://www.intercom.com/ai-bot)
- [Vercel AI SDK — Chatbot Guide](https://sdk.vercel.ai/docs/guides/multi-step-chats)
