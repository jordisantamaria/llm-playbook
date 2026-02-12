# Document Processing (書類処理)

## El Caso de Uso

Automatizar la extracción de datos de documentos: facturas, contratos, formularios, recibos.

## Dolor del Cliente

- Entrada manual de datos de facturas/recibos (手作業)
- Errores humanos en la transcripción
- Procesamiento lento de grandes volúmenes
- Coste de personal dedicado a data entry

## Cómo Funciona

```
Documento (PDF/imagen)
        ↓
┌─ Multimodal (Vision API) ─────────────────┐
│                                            │
│  Claude recibe la imagen directamente      │
│  + prompt de extracción                    │
│  + schema JSON esperado                    │
│                                            │
│  → Structured output con datos extraídos   │
└────────────────────────────────────────────┘
        ↓
Validación + almacenamiento en DB
```

## Implementación

```ts
import { generateObject } from "ai";
import { anthropic } from "@ai-sdk/anthropic";
import { z } from "zod";

const invoiceSchema = z.object({
  vendorName: z.string(),
  invoiceNumber: z.string(),
  date: z.string(),
  items: z.array(z.object({
    description: z.string(),
    quantity: z.number(),
    unitPrice: z.number(),
    total: z.number(),
  })),
  subtotal: z.number(),
  tax: z.number(),
  total: z.number(),
  currency: z.enum(["JPY", "USD", "EUR"]),
});

const { object } = await generateObject({
  model: anthropic("claude-sonnet-4-5-20250514"),
  schema: invoiceSchema,
  messages: [{
    role: "user",
    content: [
      { type: "image", image: invoiceImageBuffer },
      { type: "text", text: "Extrae todos los datos de esta factura." },
    ],
  }],
});
```

## Ventajas sobre OCR Tradicional

| | OCR Tradicional | LLM Vision |
|--|-----------------|-----------|
| Plantillas | Necesita una por formato | Entiende cualquier formato |
| Japonés + inglés mezclado | Difícil | Nativo |
| Tablas complejas | Falla a menudo | Lo entiende bien |
| Contexto | No entiende qué es cada campo | Entiende semántica |
| Setup | Configuración pesada | Un prompt |

## Aplicaciones en Japón

- **Facturas (請求書)** — extracción de datos para contabilidad
- **Recibos (領収書)** — automatizar notas de gastos (経費精算)
- **Contratos (契約書)** — extraer fechas, partes, montos, cláusulas clave
- **Formularios gubernamentales** — muchos son PDF/papel todavía

## Fuentes

- [Anthropic — Vision Guide](https://docs.anthropic.com/en/docs/build-with-claude/vision)
- [OpenAI — Vision](https://platform.openai.com/docs/guides/vision)
- [Vercel AI SDK — Multi-modal](https://sdk.vercel.ai/docs/ai-sdk-core/multi-modal)
