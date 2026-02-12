# Workflow Automation (業務自動化)

## El Caso de Uso

Agentes AI que automatizan procesos de negocio multi-paso: onboarding de empleados, procesamiento de órdenes, aprobaciones, reporting.

## Ejemplo: Procesamiento de Órdenes de Compra

```
Email de proveedor llega
        ↓
Agente:
  1. Extrae datos de la orden (structured output)
  2. Valida contra catálogo de productos (tool: search DB)
  3. Verifica presupuesto del departamento (tool: check budget)
  4. Si < ¥50,000 → aprueba automáticamente
  5. Si > ¥50,000 → envía para aprobación humana (tool: create approval request)
  6. Notifica al solicitante (tool: send notification)
```

## Implementación con Tool Use

```ts
const result = await streamText({
  model: anthropic("claude-sonnet-4-5-20250514"),
  system: `Eres un agente que procesa órdenes de compra.
Sigue este flujo:
1. Extrae datos de la orden
2. Valida el producto en el catálogo
3. Verifica el presupuesto
4. Aprueba o escala según el monto`,
  tools: {
    searchCatalog: tool({
      description: "Buscar producto en el catálogo",
      parameters: z.object({ productName: z.string() }),
      execute: async ({ productName }) => db.searchProducts(productName),
    }),
    checkBudget: tool({
      description: "Verificar presupuesto disponible del departamento",
      parameters: z.object({ departmentId: z.string() }),
      execute: async ({ departmentId }) => db.getBudget(departmentId),
    }),
    createApproval: tool({
      description: "Crear solicitud de aprobación para un manager",
      parameters: z.object({
        orderId: z.string(),
        managerId: z.string(),
        amount: z.number(),
      }),
      execute: async (params) => approvalService.create(params),
    }),
  },
  maxSteps: 8,
  messages,
});
```

## Patrón: Human-in-the-Loop

Para acciones con impacto real (enviar emails, aprobar gastos, modificar datos), **siempre pedir confirmación humana**:

```
Agente: "He analizado la orden. El producto existe en el catálogo, el presupuesto
         es suficiente (¥320,000 disponibles). ¿Apruebo la orden de ¥45,000?"
         [Aprobar] [Rechazar] [Modificar]
```

## Aplicaciones en Japón

- **経費精算 (notas de gastos)** — procesar recibos → extraer datos → aprobar/rechazar
- **入社手続き (onboarding)** — automatizar la creación de cuentas, asignación de equipo, notificaciones
- **発注処理 (órdenes de compra)** — flujo completo de validación y aprobación
- **レポート作成 (reporting)** — recopilar datos de múltiples fuentes y generar informes

## Fuentes

- [Anthropic — Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)
- [Vercel AI SDK — Multi-Step Tool Calls](https://sdk.vercel.ai/docs/ai-sdk-core/tools-and-tool-calling#multi-step-calls)
