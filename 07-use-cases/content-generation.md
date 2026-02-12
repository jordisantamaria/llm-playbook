# Content Generation (コンテンツ生成)

## El Caso de Uso

Generar contenido de marketing, traducciones, localización, emails, descripciones de producto.

## Aplicaciones

### 1. Traducción y Localización JP↔EN

No es solo traducir — es **localizar**: adaptar el tono, las formalidades (敬語 levels), y las convenciones culturales.

```ts
const { object } = await generateObject({
  model: anthropic("claude-sonnet-4-5-20250514"),
  schema: z.object({
    translation: z.string(),
    formalityLevel: z.enum(["casual", "polite", "formal", "honorific"]),
    notes: z.string().optional(),
  }),
  prompt: `Traduce al japonés con nivel de formalidad "formal" (ビジネスメール向け):
"We're excited to announce our new product launch next month."`,
});
```

### 2. Marketing Copy

Generar variaciones de textos para A/B testing, adaptar mensajes por audiencia.

### 3. Descripciones de Producto

A partir de especificaciones técnicas, generar descripciones para e-commerce.

### 4. Email Drafting

Generar borradores de emails de negocio en japonés (respetando la estructura formal de ビジネスメール).

## Valor para Empresas Japonesas

- **Empresas que se expanden al extranjero** — necesitan contenido en inglés de calidad
- **Empresas extranjeras en Japón** — necesitan localización al japonés natural
- **E-commerce** — descripciones de producto en múltiples idiomas
- **Marketing** — generar variaciones rápidamente

## Limitaciones Importantes

- **Siempre necesita revisión humana** — el LLM puede generar contenido incorrecto
- **Marca y tono** — el LLM necesita contexto sobre la voz de la marca
- **Datos sensibles** — no enviar información confidencial del cliente a APIs externas

## Fuentes

- [Anthropic — Content Generation Use Case](https://docs.anthropic.com/en/docs/about-claude/use-case-guides)
- [DeepL API](https://www.deepl.com/pro-api) — alternativa especializada para traducción
