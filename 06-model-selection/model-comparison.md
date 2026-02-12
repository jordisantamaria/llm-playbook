# Comparación de Modelos

## Matriz de decisión rápida

| Need | Best Model | Why |
|------|-----------|-----|
| Mejor calidad general | Claude Opus 4 | Razonamiento más fuerte, el mejor para tareas complejas |
| Mejor equilibrio velocidad/calidad | Claude Sonnet 4.5 o GPT-4o | Rápido, capaz, coste razonable |
| Más barato para tareas simples | Claude Haiku 4.5 o GPT-4o-mini | Classification, routing, extraction |
| Contexto más largo | Gemini 2.5 Pro (1M tokens) | Documentos enormes, muchos archivos |
| Razonamiento complejo | Claude Opus 4 o o3 | Matemáticas, lógica, planificación |
| Generación de código | Claude Sonnet 4.5 / Opus 4 | Top en benchmarks de código |
| Comprensión de imágenes | Claude Sonnet 4.5 o GPT-4o | Ambos fuertes, Claude mejor para documentos |
| Idioma japonés | Claude Sonnet 4.5 o GPT-4o | Ambos manejan bien el japonés |
| Embeddings | OpenAI text-embedding-3-small | Mejor precio/rendimiento, Claude no tiene modelo de embeddings |

## Comparación detallada

### Claude (Anthropic)

| Model | Context | Input $/1M | Output $/1M | Strengths |
|-------|---------|-----------|-------------|-----------|
| Opus 4 | 200K | $15 | $75 | Mejor razonamiento, análisis complejo |
| Sonnet 4.5 | 200K | $3 | $15 | Mejor equilibrio, genial para producción |
| Haiku 4.5 | 200K | $0.80 | $4 | Rápido y barato, classification |

- Lo mejor: seguir instrucciones complejas, análisis de documentos, código, seguridad
- Único: extended thinking, prompt caching (90% de ahorro), comprensión de XML tags
- Limitación: no tiene modelo de embeddings (usa OpenAI o Voyage)

### OpenAI

| Model | Context | Input $/1M | Output $/1M | Strengths |
|-------|---------|-----------|-------------|-----------|
| GPT-4o | 128K | $2.50 | $10 | Propósito general, buena visión |
| GPT-4o-mini | 128K | $0.15 | $0.60 | Extremadamente barato |
| o3 | 200K | $10 | $40 | Razonamiento profundo, matemáticas |

- Lo mejor: conocimiento amplio, ecosistema (comunidad más grande), integración con Azure
- Único: modelos de embeddings nativos, JSON mode, integración con DALL-E
- Limitación: contexto más pequeño que Gemini, razonamiento por debajo de Claude Opus en tareas complejas

### Google Gemini

| Model | Context | Input $/1M | Output $/1M | Strengths |
|-------|---------|-----------|-------------|-----------|
| Gemini 2.5 Pro | 1M | $1.25-$2.50 | $5-$10 | Contexto enorme, multimodal |
| Gemini 2.5 Flash | 1M | $0.15 | $0.60 | Muy barato, rápido |

- Lo mejor: contexto extremadamente largo (codebases enteras), multimodal (vídeo, audio)
- Único: contexto de 1M tokens, Google Search grounding
- Limitación: calidad menos consistente que Claude/GPT para instrucciones complejas

## Cómo recomendar a clientes

**"Qué modelo deberíamos usar?"** -- la pregunta más común de los clientes.

Framework de respuesta:
1. **Cuál es la tarea?** -- determina la capacidad necesaria
2. **Cuál es el volumen?** -- determina la sensibilidad al coste
3. **Cuál es el requisito de latencia?** -- determina la velocidad necesaria
4. **Cuál es el requisito de precisión?** -- determina el tier del modelo

Ejemplo: "Para tu chatbot de atención al cliente procesando 10K mensajes/mes, yo usaría Claude Sonnet 4.5 por calidad a un coste razonable (~$135/mes). Para la capa simple de routing de FAQ, Haiku a ~$10/mes."

## Fuentes

- [Anthropic Pricing](https://www.anthropic.com/pricing)
- [OpenAI Pricing](https://openai.com/api/pricing/)
- [Google AI Pricing](https://ai.google.dev/pricing)
- [Chatbot Arena Leaderboard](https://lmarena.ai/) -- rankings de modelos por crowdsourcing
- [Artificial Analysis](https://artificialanalysis.ai/) -- benchmarks y precios independientes
