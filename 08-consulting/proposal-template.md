# Proposal Template (提案書テンプレート)

## Estructura de una Propuesta

Después de la 商談, envías esto al cliente. Debe ser claro, conciso, y profesional.

---

## [Nombre del Proyecto]

### 1. Resumen Ejecutivo

[2-3 frases. Qué se va a hacer y qué resultado se espera.]

> Construir un asistente AI interno que permita a los empleados consultar la documentación de la empresa en lenguaje natural, reduciendo el tiempo de búsqueda de información en un 70% estimado.

### 2. Problema Actual

[Lo que el cliente te contó en la 商談. Usa sus propias palabras.]

- Los empleados dedican ~X horas/semana buscando información en [sistemas]
- Los documentos están dispersos en [Confluence/SharePoint/etc.]
- Los nuevos empleados tardan [X semanas] en ser productivos

### 3. Solución Propuesta

[Descripción a alto nivel. Sin jerga técnica excesiva.]

- Chatbot accesible desde [Slack/web/Teams]
- Responde preguntas basándose en los documentos internos
- Cita la fuente de cada respuesta para que el usuario pueda verificar
- Soporta japonés e inglés

**Stack técnico:** [Solo si el cliente es técnico]
- Frontend: Next.js
- AI: Claude API (Anthropic)
- Base de datos: PostgreSQL con búsqueda vectorial

### 4. Fases del Proyecto

| Fase | Duración | Entregable |
|------|----------|-----------|
| **Discovery** | 1 semana | Análisis de datos, definición de scope |
| **MVP** | 3 semanas | Prototipo funcional con 50 documentos |
| **Validación** | 1 semana | Testing con usuarios reales, métricas |
| **Producción** | 2 semanas | Deploy, integración, documentación |
| **Total** | **7 semanas** | |

### 5. Inversión

| Concepto | Coste |
|---------|-------|
| Desarrollo (7 semanas × X h/semana × tarifa) | ¥X |
| Costes de API (estimado mensual) | ¥X/mes |
| Infraestructura (estimado mensual) | ¥X/mes |
| **Total setup** | **¥X** |
| **Coste mensual operativo** | **¥X/mes** |

### 6. ROI Estimado

| Métrica | Antes | Después |
|---------|-------|---------|
| Tiempo buscando información | X h/semana/persona | X h/semana/persona |
| Productividad nuevos empleados | X semanas | X semanas |
| Coste equivalente | ¥X/mes | ¥X/mes |
| **Ahorro estimado** | | **¥X/mes** |

### 7. Próximos Pasos

1. Confirmar scope y acceso a datos
2. Kick-off de la fase Discovery
3. Review semanal de progreso

### 8. Sobre Mí

[Tu background, proyectos relevantes, links a GitHub.]

---

## Tips para Propuestas en Japón

- **Sé formal** — usa です/ます体 si escribes en japonés
- **Incluye timeline claro** — las empresas japonesas quieren saber exactamente cuándo
- **MVP primero** — propón algo pequeño y rápido para reducir el riesgo percibido
- **ROI concreto** — números > promesas vagas
- **No vendas tecnología** — vende resultado de negocio
- **PDF** — la propuesta final en PDF se percibe más profesional que un Google Doc

## Fuentes

- [The Mom Test](https://www.momtestbook.com/) — cómo validar ideas con clientes
- [Anthropic — Use Case Examples](https://docs.anthropic.com/en/docs/about-claude/use-case-guides)
