# Code Assistant (コードアシスタント)

## El Caso de Uso

Herramientas AI que ayudan a equipos de desarrollo: code review, documentación automática, generación de tests, explicación de legacy code.

## Aplicaciones Prácticas

### 1. Code Review Automático

El LLM revisa PRs y sugiere mejoras antes del review humano.

```ts
const review = await generateObject({
  model: anthropic("claude-sonnet-4-5-20250514"),
  schema: z.object({
    issues: z.array(z.object({
      severity: z.enum(["critical", "warning", "suggestion"]),
      file: z.string(),
      line: z.number().optional(),
      description: z.string(),
      suggestion: z.string(),
    })),
    summary: z.string(),
  }),
  prompt: `Revisa este diff de código y encuentra problemas:
\`\`\`diff
${gitDiff}
\`\`\``,
});
```

### 2. Documentación Automática de Legacy Code

Muy valioso en Japón donde hay mucho legacy code (COBOL, Java antiguo).

### 3. Generación de Tests

El LLM genera unit tests a partir de código existente.

### 4. Chatbot sobre el Codebase (RAG sobre código)

Indexar el repositorio y permitir preguntas: "¿Dónde se gestiona la autenticación?", "¿Cómo funciona el pipeline de pagos?"

## Propuesta para Empresas Japonesas

El legacy code es un problema enorme en Japón. Muchas empresas tienen:
- Sistemas COBOL/Java que nadie entiende
- Documentación desactualizada o inexistente
- Desarrolladores senior que se jubilan (2025年問題)

**Propuesta:** "Le pongo un chatbot encima de su codebase para que los desarrolladores nuevos puedan entender el sistema sin depender de los seniors."

## Fuentes

- [Anthropic — Coding Use Cases](https://docs.anthropic.com/en/docs/about-claude/use-case-guides/coding-agent)
- [GitHub Copilot](https://github.com/features/copilot) — referencia del mercado
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — agente de coding de Anthropic
