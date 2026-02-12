# Data Extraction (データ抽出)

## El Caso de Uso

Convertir datos no estructurados (emails, PDFs, páginas web, texto libre) en datos estructurados almacenables en una base de datos.

## Diferencia con Document Processing

- **Document processing** = extraer datos de documentos visuales (facturas, formularios)
- **Data extraction** = extraer datos de cualquier texto (emails, logs, web scraping, CSV desordenados)

## Ejemplo: Extracción de Emails

```ts
const emailSchema = z.object({
  sender: z.string(),
  intent: z.enum(["inquiry", "order", "complaint", "information"]),
  products: z.array(z.string()),
  urgency: z.enum(["low", "medium", "high"]),
  requestedAction: z.string().optional(),
});

const { object } = await generateObject({
  model: anthropic("claude-haiku-4-5-20241022"), // Haiku es suficiente para extracción
  schema: emailSchema,
  prompt: `Extrae información estructurada de este email de cliente:
"${emailContent}"`,
});

// Guardar en DB
await db.insert(customerRequests).values(object);
```

## Ejemplo: Web Scraping + Estructuración

```ts
// 1. Scrape (con Cheerio, Playwright, etc.)
const rawHtml = await fetchPage(url);

// 2. Extraer datos con LLM
const { object } = await generateObject({
  model: anthropic("claude-haiku-4-5-20241022"),
  schema: z.object({
    companyName: z.string(),
    industry: z.string(),
    employees: z.number().optional(),
    location: z.string(),
    description: z.string(),
  }),
  prompt: `Extrae información de esta empresa a partir de su web:
${rawText}`,
});
```

## Aplicaciones en Japón

- **Lead generation** — extraer datos de empresas de directorios web
- **Procesamiento de emails** — clasificar y extraer datos de inquiries
- **ETL de datos legacy** — convertir CSVs/Excel desordenados en datos limpios
- **Análisis de feedback** — extraer temas y sentimiento de encuestas de texto libre

## Coste

Haiku es suficiente para la mayoría de extracciones → muy barato (~$0.80/1M input tokens).
10,000 emails/mes ≈ $5-10/mes.

## Fuentes

- [Anthropic — Text Extraction](https://docs.anthropic.com/en/docs/about-claude/use-case-guides)
- [Vercel AI SDK — generateObject](https://sdk.vercel.ai/docs/ai-sdk-core/generating-structured-data)
