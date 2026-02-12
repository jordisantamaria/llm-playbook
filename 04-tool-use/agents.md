# Agents

## Qué es un Agent

Un agent es un LLM en un **loop** que puede usar tools, observar resultados y decidir qué hacer a continuación — hasta que la tarea esté completa.

```
          ┌──────────────────────────────────┐
          │                                  │
User  →  LLM decides action  →  Execute tool  →  Observe result
          │                                  │
          │  Task complete? ──── No ─────────┘
          │       │
          │      Yes
          │       ↓
          └──  Final answer
```

Un chatbot responde preguntas. Un agent **completa tareas**.

## Agent Loop (conceptual)

```ts
async function agentLoop(userMessage: string, maxSteps = 10) {
  const messages = [{ role: "user", content: userMessage }];

  for (let step = 0; step < maxSteps; step++) {
    const response = await anthropic.messages.create({
      model: "claude-sonnet-4-5-20250514",
      max_tokens: 4096,
      tools,
      messages,
    });

    // Add assistant response to history
    messages.push({ role: "assistant", content: response.content });

    // If the model wants to use a tool
    if (response.stop_reason === "tool_use") {
      const toolUse = response.content.find((c) => c.type === "tool_use");
      const result = await executeTool(toolUse.name, toolUse.input);

      // Add tool result to history
      messages.push({
        role: "user",
        content: [{ type: "tool_result", tool_use_id: toolUse.id, content: JSON.stringify(result) }],
      });
      continue; // LLM decides next step
    }

    // Model responded with text — task is done
    return response.content[0].text;
  }

  return "Max steps reached";
}
```

## Vercel AI SDK — maxSteps

El SDK se encarga del agent loop por ti:

```ts
const result = streamText({
  model: anthropic("claude-sonnet-4-5-20250514"),
  tools: {
    searchDB: tool({ /* ... */ }),
    sendEmail: tool({ /* ... */ }),
    createTicket: tool({ /* ... */ }),
  },
  maxSteps: 5, // LLM can chain up to 5 tool calls
  messages,
});
```

## Patrones de Agents

### ReAct (Reasoning + Acting)

El modelo piensa, luego actúa, luego observa:

```
Thought: The user wants to find orders from last week. I need to search the database.
Action: searchOrders({ dateFrom: "2024-03-01", dateTo: "2024-03-07" })
Observation: Found 3 orders: [...]
Thought: I have the orders. The user also asked about total revenue. I need to calculate.
Action: calculateTotal({ orders: [...] })
Observation: Total: ¥450,000
Thought: I have all the information needed.
Answer: Last week you had 3 orders totaling ¥450,000.
```

### Human-in-the-Loop

El agent propone acciones, el humano aprueba antes de ejecutar:

```ts
// Agent suggests sending an email
if (toolUse.name === "send_email") {
  // Show to user for approval before executing
  const approved = await askUserApproval(toolUse.input);
  if (!approved) {
    // Return rejection as tool result
    messages.push({
      role: "user",
      content: [{ type: "tool_result", tool_use_id: toolUse.id, content: "User rejected this action." }],
    });
    continue;
  }
}
```

**Crítico para producción** — nunca quieres que un agent envíe emails o modifique datos sin aprobación.

## Consideraciones clave

| Concern | Solution |
|---------|----------|
| **Runaway loops** | Pon maxSteps, implementa timeout |
| **Dangerous actions** | Human-in-the-loop para escrituras/envíos |
| **Cost control** | Cada step = API call = tokens = dinero |
| **Error handling** | Devuelve el error como tool result, deja que el LLM se adapte |
| **Observability** | Loguea cada step: tool llamado, input, output, tiempo |

## Fuentes

- [Anthropic — Building Agents](https://docs.anthropic.com/en/docs/build-with-claude/agentic)
- [Vercel AI SDK — Multi-Step Calls](https://sdk.vercel.ai/docs/ai-sdk-core/tools-and-tool-calling#multi-step-calls)
- [ReAct Paper](https://arxiv.org/abs/2210.03629)
- [Anthropic — Agent Design Patterns](https://www.anthropic.com/engineering/building-effective-agents)
