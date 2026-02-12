# Architecture Patterns (アーキテクチャパターン)

## Patrones Comunes en Aplicaciones AI

### 1. RAG (Retrieval Augmented Generation)

El patrón más común. El LLM responde basándose en documentos del cliente.

```
Documentos → Embeddings → Vector DB
                                ↓
User question → Embed → Search → Top-k chunks → LLM → Respuesta
```

**Cuándo usarlo:** Knowledge base interna, Q&A sobre documentos, soporte al cliente
**Ver:** [03-rag/rag-pipeline.md](../03-rag/rag-pipeline.md)

### 2. Agent Loop

El LLM decide qué acciones tomar en un loop hasta completar la tarea.

```
User request → LLM → Tool call → Observe → LLM → Tool call → ... → Final answer
```

**Cuándo usarlo:** Workflow automation, tareas multi-paso, cuando el LLM necesita interactuar con sistemas
**Ver:** [04-tool-use/agents.md](../04-tool-use/agents.md)

### 3. Classification + Routing

Un modelo barato clasifica la request y la envía al pipeline adecuado.

```
Input → Classifier (Haiku) → Route A: Simple → Fast response
                            → Route B: Complex → RAG pipeline
                            → Route C: Action → Agent loop
                            → Route D: Escalate → Human
```

**Cuándo usarlo:** Customer support, cualquier sistema que maneja diferentes tipos de requests

### 4. Human-in-the-Loop

El agente propone acciones, un humano aprueba antes de ejecutar.

```
Input → LLM propone acción → Mostrar al usuario → [Aprobar/Rechazar] → Ejecutar
```

**Cuándo usarlo:** Cualquier acción con impacto real (enviar emails, modificar datos, aprobar gastos)
**Regla:** En producción, todo lo que tiene side-effects necesita aprobación humana.

### 5. Map-Reduce (Procesamiento por lotes)

Procesar muchos items en paralelo, luego combinar resultados.

```
100 documentos → Map: Resumir cada uno (en paralelo) → Reduce: Combinar resúmenes → Resumen final
```

**Cuándo usarlo:** Procesar grandes volúmenes de documentos, análisis de datos masivo

### 6. Prompt Chain

Encadenar múltiples llamadas al LLM donde el output de una es input de la siguiente.

```
Step 1: Extraer datos → Step 2: Validar → Step 3: Clasificar → Step 4: Formatear
```

**Cuándo usarlo:** Tareas complejas que se dividen mejor en pasos especializados.
Cada paso puede usar un prompt diferente optimizado para esa subtarea.

## Cómo Elegir

```
¿El LLM necesita acceso a datos del cliente? → RAG
¿El LLM necesita ejecutar acciones? → Agent Loop
¿Hay diferentes tipos de requests? → Classification + Routing
¿Las acciones tienen impacto real? → Human-in-the-Loop
¿Hay que procesar muchos items? → Map-Reduce
¿La tarea tiene pasos claros y secuenciales? → Prompt Chain
```

En la práctica, la mayoría de apps combinan varios patrones. Un customer support bot típico usa Classification + RAG + Human-in-the-Loop.

## Fuentes

- [Anthropic — Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)
- [LangChain — Architecture Patterns](https://js.langchain.com/docs/concepts/)
- [Vercel AI SDK — Patterns](https://sdk.vercel.ai/docs/guides)
