# Streaming

## Por qué importa el Streaming

- Sin streaming: el usuario espera 3-10 segundos mirando un spinner
- Con streaming: los tokens aparecen a medida que se generan, se siente instantáneo
- **No negociable para cualquier chat UI en producción**

## Cómo Funciona

Las APIs de LLMs usan **Server-Sent Events (SSE)** -- el servidor envía chunks de la respuesta a medida que se generan.

```
Client request → Server starts generating → Token 1 → Token 2 → Token 3 → ... → [DONE]
```

## Vercel AI SDK (Enfoque Recomendado)

La forma más simple de implementar streaming en Next.js:

### API Route (Server)

```ts
// app/api/chat/route.ts
import { anthropic } from "@ai-sdk/anthropic";
import { streamText } from "ai";

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: anthropic("claude-sonnet-4-5-20250514"),
    system: "You are a helpful assistant.",
    messages,
  });

  return result.toDataStreamResponse();
}
```

### Client Component

```tsx
// components/chat.tsx
"use client";
import { useChat } from "@ai-sdk/react";

export function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();

  return (
    <div>
      {messages.map((m) => (
        <div key={m.id}>
          <strong>{m.role}:</strong> {m.content}
        </div>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={handleInputChange} />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

Eso es todo. `useChat` se encarga de:
- Enviar los mensajes a la API route
- Hacer streaming de la respuesta token a token
- Gestionar el historial de conversación
- Estados de carga

## Cambiar de Proveedor en 1 Línea

```ts
// Change from Claude to OpenAI:
import { openai } from "@ai-sdk/openai";
const result = streamText({
  model: openai("gpt-4o"),  // only this line changes
  // ...
});
```

## Puntos Clave

- Vercel AI SDK abstrae toda la complejidad de SSE
- El hook `useChat` gestiona todo el ciclo de vida del chat en el cliente
- El historial de conversación se envía automáticamente con cada petición
- Funciona con Claude, OpenAI, Gemini, Mistral -- mismo código

## Fuentes

- [Vercel AI SDK - Streaming](https://sdk.vercel.ai/docs/ai-sdk-core/generating-text#streamtext)
- [Vercel AI SDK - useChat](https://sdk.vercel.ai/docs/ai-sdk-ui/chatbot)
- [MDN - Server-Sent Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)
- [Anthropic - Streaming Messages](https://docs.anthropic.com/en/api/messages-streaming)
