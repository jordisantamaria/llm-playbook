# How LLMs Work

## Tokens

- Los LLMs no leen texto directamente -- leen **tokens** (unidades de subpalabra)
- ~1 token = ~4 caracteres en inglés, ~1-2 caracteres en japonés
- Cada llamada a la API tiene **input tokens** (lo que envías) y **output tokens** (lo que genera el modelo)
- Pagas por token -- este es el principal factor de coste

## Context Window

- El número máximo de tokens que el modelo puede procesar en una sola petición
- Incluye: system prompt + historial de conversación + mensaje del usuario + respuesta del modelo
- Si te pasas del límite, los mensajes más antiguos se truncan o la petición falla

| Model | Context Window |
|-------|---------------|
| Claude Sonnet 4.5 | 200K tokens |
| Claude Opus 4 | 200K tokens |
| GPT-4o | 128K tokens |
| Gemini 2.5 Pro | 1M tokens |

## Temperature

- Controla la aleatoriedad del output (0.0 a 1.0+)
- `temperature: 0` -- determinista, mismo input = (casi) mismo output. Úsalo para: extracción de datos, clasificación, structured output
- `temperature: 0.7` -- creatividad equilibrada. Úsalo para: chatbots, conversación general
- `temperature: 1.0+` -- muy creativo/aleatorio. Úsalo para: brainstorming, escritura creativa

## Top-p (Nucleus Sampling)

- Alternativa a temperature para controlar la aleatoriedad
- `top_p: 0.9` -- el modelo considera los tokens que componen el 90% de la masa de probabilidad
- En general: usa temperature O top-p, no ambos a la vez
- En la práctica, temperature es más intuitivo y se usa más

## Key Mental Model

```
User prompt (tokens) → Model processes → Output tokens (streamed or complete)
                        ↑
              System prompt + history
              (all counts against context window)
```

## Implicaciones Prácticas

- **Los system prompts largos se comen tu context window** -- mantenlos concisos
- **El texto en japonés usa ~2x más tokens** que el inglés para el mismo contenido -- tenlo en cuenta para estimaciones de coste con clientes JP
- **Context window ≠ memoria** -- el modelo no tiene memoria entre peticiones, tienes que reenviar el historial de conversación cada vez

## Fuentes

- [Anthropic - Tokens](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching#token-counting)
- [OpenAI - Tokenizer](https://platform.openai.com/tokenizer)
- [Anthropic - Model comparison](https://docs.anthropic.com/en/docs/about-claude/models)
- [OpenAI - API Reference (temperature, top_p)](https://platform.openai.com/docs/api-reference/chat/create)
