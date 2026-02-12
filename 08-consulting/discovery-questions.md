# Discovery Questions (商談での質問)

## Objetivo

En la primera reunión, tu trabajo no es vender — es **entender el problema**. Las preguntas correctas te posicionan como consultor, no como developer que busca trabajo.

## Preguntas sobre el Problema

### Entender el dolor actual
- "¿Cuál es el proceso manual que más tiempo consume en su equipo?"
- "¿Cuántas personas están dedicadas a esta tarea actualmente?"
- "¿Cuánto tiempo dedican por semana/mes?"
- "¿Qué errores ocurren más frecuentemente en este proceso?"

### Entender los datos
- "¿Qué tipo de documentos/datos manejan?" (PDFs, emails, CSVs, bases de datos)
- "¿En qué idioma(s) están los datos?"
- "¿Cuánto volumen procesan?" (documentos/mes, consultas/día)
- "¿Dónde están almacenados actualmente?" (SharePoint, S3, bases de datos locales)
- "¿Hay datos sensibles que no pueden salir de su infraestructura?"

### Entender el output esperado
- "¿Qué aspecto tendría la solución ideal?"
- "¿Quién usaría esta herramienta?" (empleados internos, clientes finales)
- "¿Necesitan que funcione en japonés, inglés, o ambos?"
- "¿Qué nivel de precisión necesitan?" (¿es aceptable 90%? ¿necesitan 99%?)

## Preguntas Técnicas

### Infraestructura
- "¿Qué cloud usan?" (AWS, Azure, GCP, on-premise)
- "¿Tienen restricciones de seguridad o compliance?" (ISMS, Pマーク, SOC2)
- "¿Pueden usar APIs externas o necesitan todo on-premise?"
- "¿Tienen un equipo de desarrollo interno?"

### Experiencia previa con AI
- "¿Han probado alguna solución AI antes?"
- "¿Qué funcionó y qué no?"
- "¿Tienen expectativas específicas sobre qué modelo usar?" (a veces el cliente ya tiene una opinión)

## Preguntas de Negocio

### Presupuesto y timeline
- "¿Tienen un presupuesto definido para este proyecto?"
- "¿Cuál es el timeline ideal?" (MVP en 1 mes, producción en 3 meses)
- "¿Prefieren un MVP rápido para validar o un proyecto completo?"

### Éxito
- "¿Cómo medirían el éxito de este proyecto?"
- "¿Qué KPIs son importantes?" (tiempo ahorrado, errores reducidos, satisfacción del cliente)

## Preguntas Trampa que el Cliente Puede Hacer

| Pregunta del cliente | Cómo responder |
|---------------------|---------------|
| "¿Puede la AI reemplazar a nuestros empleados?" | "La AI es mejor como herramienta que asiste a su equipo, no como reemplazo. Automatiza las tareas repetitivas para que su equipo se enfoque en trabajo de mayor valor." |
| "¿Podemos usar ChatGPT directamente?" | "ChatGPT es excelente para uso individual, pero para integrar AI en sus procesos de negocio necesitamos una solución custom que se conecte a sus datos y sistemas." |
| "¿Cuánto cuesta la API de OpenAI/Claude?" | Dar un ejemplo concreto con su volumen estimado. "Para 10,000 consultas/mes, estimamos ~¥10,000/mes en costes de API." |
| "¿Es seguro enviar nuestros datos?" | "Las APIs de Claude y OpenAI no entrenan con sus datos. Además, podemos usar Azure OpenAI o AWS Bedrock donde los datos no salen de su infraestructura." |

## Framework de la Reunión

```
1. Escuchar el problema (15 min)
   → No interrumpir, tomar notas

2. Preguntas de clarificación (15 min)
   → Usar las preguntas de arriba

3. Propuesta inicial a alto nivel (10 min)
   → "Basándome en lo que me cuentan, creo que podríamos..."
   → No ser demasiado específico aún

4. Próximos pasos (5 min)
   → "Le preparo una propuesta detallada con estimación de costes y timeline"
```

## Fuentes

- [Anthropic — Use Case Guide](https://docs.anthropic.com/en/docs/about-claude/use-case-guides)
- [The Mom Test (libro)](https://www.momtestbook.com/) — cómo hacer preguntas que revelan la verdad
