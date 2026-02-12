# ROI Framework

## Cómo Justificar la Inversión

El cliente no compra "AI" — compra **ahorro de tiempo, reducción de errores, o nueva capacidad**. Tu trabajo es cuantificarlo.

## Fórmula ROI

```
ROI = (Beneficio mensual - Coste mensual) / Coste mensual × 100%

Beneficio = Ahorro de tiempo + Reducción de errores + Ingresos nuevos
Coste = Tu tarifa de desarrollo + Costes de API + Infraestructura + Mantenimiento
```

## Tipos de Beneficio

### 1. Ahorro de Tiempo (el más fácil de cuantificar)

```
Personas afectadas × Horas ahorradas/semana × Coste/hora × 4 semanas

Ejemplo: Knowledge base interna
- 100 empleados usan el sistema
- Cada uno ahorra 30 min/semana buscando información
- Coste promedio por hora: ¥3,000
- Ahorro: 100 × 0.5h × ¥3,000 × 4 = ¥600,000/mes
```

### 2. Reducción de Errores

```
Errores/mes × Coste por error

Ejemplo: Extracción de facturas
- 500 facturas/mes procesadas manualmente
- Tasa de error: 5% = 25 errores/mes
- Coste por error (corrección + impacto): ¥10,000
- Ahorro: 25 × ¥10,000 = ¥250,000/mes
```

### 3. Capacidad Nueva

Más difícil de cuantificar pero a menudo el más valioso:
- "Ahora pueden atender clientes 24/7" (antes solo 9-18)
- "Ahora pueden procesar 10x más documentos sin contratar"
- "Ahora los nuevos empleados son productivos en 1 semana en vez de 1 mes"

## Ejemplo Completo: Chatbot de Soporte

### Costes

| Concepto | Mensual |
|---------|---------|
| Tu tarifa (mantenimiento, 10h/mes) | ¥500,000 |
| API (Claude Sonnet) | ¥17,000 |
| Infraestructura | ¥7,000 |
| **Total coste** | **¥524,000/mes** |

### Beneficios

| Concepto | Mensual |
|---------|---------|
| 60% menos tickets a agentes humanos (3 agentes × 60% × ¥300,000) | ¥540,000 |
| Disponibilidad 24/7 (captura 10% más consultas fuera de horario) | ¥150,000 |
| Reducción de tiempo de respuesta (mejora satisfacción → retención) | Difícil de cuantificar |
| **Total beneficio** | **≥ ¥690,000/mes** |

### ROI

```
ROI = (¥690,000 - ¥524,000) / ¥524,000 × 100% = 31.7%
Payback: Inmediato (beneficio > coste desde el mes 1)
```

## Cómo Presentarlo

No uses una hoja de cálculo aburrida. En la propuesta:

1. **El problema** — "Su equipo dedica X horas/mes a Y tarea"
2. **El coste actual** — "Esto equivale a ¥Z/mes"
3. **La solución** — "Un sistema AI que automatiza el 60-70% de esta tarea"
4. **La inversión** — "Coste total: ¥X/mes (desarrollo + API + infra)"
5. **El retorno** — "Ahorro estimado: ¥Y/mes. ROI positivo desde el mes 1"
6. **El riesgo mitigado** — "Empezamos con un MVP de 1 mes para validar los números"

## Fuentes

- [McKinsey — The Economic Potential of Generative AI](https://www.mckinsey.com/capabilities/mckinsey-digital/our-insights/the-economic-potential-of-generative-ai-the-next-productivity-frontier)
- [a16z — AI in the Enterprise](https://a16z.com/ai/)
