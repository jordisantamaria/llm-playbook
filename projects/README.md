# Projects

Repos separados para cada proyecto. Aquí solo links de referencia.

## Proyecto 1: AI Chatbot
- **Repo:** [ai-chatbot](https://github.com/jordisantamaria/ai-chatbot)
- **Stack:** Next.js + Vercel AI SDK + Claude API
- **Objetivo:** Chatbot con streaming, historial de conversación
- **Conceptos:** Streaming, Messages API, conversation history

## Proyecto 2: Document Q&A (RAG)
- **Repo:** [document-qa](https://github.com/jordisantamaria/document-qa)
- **Stack:** Next.js + Claude API + pgvector + OpenAI Embeddings
- **Objetivo:** App de Q&A sobre documentos PDF
- **Conceptos:** RAG pipeline, chunking, embeddings, citación de fuentes

## Proyecto 3: AI Agent
- **Repo:** [ai-agent-toolkit](https://github.com/jordisantamaria/ai-agent-toolkit)
- **Stack:** Next.js + Claude API (tool use) + PostgreSQL + APIs externas
- **Objetivo:** Asistente que ejecuta acciones (buscar DB, llamar APIs, cálculos)
- **Conceptos:** Function calling, agent loop, human-in-the-loop

## Proyecto 4: Extracción de Facturas (請求書処理)
- **Repo:** [invoice-extractor](https://github.com/jordisantamaria/invoice-extractor)
- **Stack:** Next.js + Claude API (vision) + Zod (structured output)
- **Objetivo:** Sistema que extrae datos de facturas en cualquier formato (PDF, imagen, escaneado) con validación automática y revisión humana
- **Conceptos:** Vision API, structured output, validación matemática, cascading (Haiku → Sonnet), human-in-the-loop con UI de revisión
- **Demo:** 5-6 facturas fake en diferentes formatos, incluida una con error a propósito para demostrar la detección automática
- **Impacto en 商談:** "Le meto un PDF y salen los datos" — visual e inmediato

## Proyecto 5: Customer Support Bot (カスタマーサポート)
- **Repo:** [support-bot](https://github.com/jordisantamaria/support-bot)
- **Stack:** Next.js + Claude API + pgvector + OpenAI Embeddings + chat widget
- **Objetivo:** Chatbot de soporte embebible en la web del cliente, con conocimiento de la empresa, citación de fuentes, y escalado a humano
- **Conceptos:** RAG aplicado a soporte, chat widget embebible, citación de fuentes, escalado a humano cuando no sabe, respuestas en japonés
- **Diferencia con Proyecto 2:** UX de producción (widget, no app interna), enfocado en un caso de uso concreto y vendible
- **Demo:** Web ficticia de empresa con widget de chat que responde preguntas sobre productos/políticas basándose en documentos indexados

## Proyecto 6: Clasificador de Emails/Tickets (メール分類)
- **Repo:** [email-classifier](https://github.com/jordisantamaria/email-classifier)
- **Stack:** Next.js + Claude API (structured output) + Zod
- **Objetivo:** Emails entran → LLM clasifica urgencia, categoría, departamento → enruta automáticamente
- **Conceptos:** Structured output, clasificación, model routing (Haiku para clasificar, barato)
- **Demo:** Dashboard con 10-15 emails de ejemplo que se clasifican en tiempo real
- **El más rápido de construir** — buen ratio esfuerzo/impacto

## Proyecto 7: Resumen de Reuniones (議事録作成)
- **Repo:** [meeting-summarizer](https://github.com/jordisantamaria/meeting-summarizer)
- **Stack:** Next.js + Claude API + Whisper API (transcripción de audio)
- **Objetivo:** Audio/texto de reunión → resumen estructurado + action items + decisiones + asignación de responsables
- **Conceptos:** Procesamiento de texto largo, structured output, transcripción de audio (Whisper)
- **Relevancia en Japón:** Cultura de reuniones largas, actas detalladas (議事録) son obligatorias en muchas empresas
- **Demo:** Grabación de reunión ficticia → resumen automático con action items
