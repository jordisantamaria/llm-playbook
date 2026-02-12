# Chunking Strategies

## Por qué hacer chunking

Los documentos son demasiado largos para caber en un solo embedding o context window. Los divides en trozos más pequeños (chunks) que se pueden embeber y recuperar individualmente.

## Comparación de estrategias

### Fixed Size (la más simple)

Divide cada N caracteres/tokens con overlap:

```ts
function chunkBySize(text: string, size = 1000, overlap = 200): string[] {
  const chunks: string[] = [];
  for (let i = 0; i < text.length; i += size - overlap) {
    chunks.push(text.slice(i, i + size));
  }
  return chunks;
}
```

- **Pros:** Simple, tamaños de chunk predecibles
- **Contras:** Puede cortar a mitad de frase, pierde contexto
- **Úsalo cuando:** Prototipo rápido, texto uniforme

### By Paragraph / Section

Divide en límites naturales (saltos de línea, headers):

```ts
function chunkByParagraph(text: string): string[] {
  return text
    .split(/\n\n+/)
    .filter((chunk) => chunk.trim().length > 50);
}
```

- **Pros:** Preserva el contexto natural
- **Contras:** Tamaños de chunk desiguales
- **Úsalo cuando:** Documentos bien estructurados (markdown, artículos)

### Recursive Character Splitting

Prueba múltiples separadores en orden de prioridad:

```
Separators: ["\n\n", "\n", ". ", " "]
1. Try splitting by double newline (paragraphs)
2. If chunks are still too big, split by single newline
3. If still too big, split by sentence
4. Last resort: split by word
```

- **Pros:** Mejor equilibrio entre preservación de contexto y control de tamaño
- **Contras:** Más complejo de implementar
- **Úsalo cuando:** Sistemas RAG en producción
- Esto es lo que hace el `RecursiveCharacterTextSplitter` de LangChain

### Semantic Chunking

Usa embeddings para encontrar los límites naturales de cada tema:

- Embebe ventanas deslizantes de texto
- Cuando la similitud entre ventanas consecutivas cae, eso es un límite de chunk
- **Pros:** Chunks de mejor calidad
- **Contras:** Caro (necesitas embeber todo dos veces), lento
- **Úsalo cuando:** Documentos de alto valor donde la precisión importa

## Parámetros

| Parameter | Typical Value | Effect |
|-----------|--------------|--------|
| **Chunk size** | 500-1500 chars | Smaller = more precise retrieval, larger = more context |
| **Overlap** | 10-20% of chunk size | Prevents losing context at boundaries |
| **Top-k** | 3-10 chunks | How many chunks to retrieve per query |

## Reglas clave

1. **El overlap es importante** — sin él, pierdes contexto en los límites de los chunks
2. **Incluye metadata** — guarda el fichero de origen, número de página y título de sección con cada chunk
3. **Testea con queries reales** — la mejor estrategia de chunking depende de tus documentos y consultas
4. **Texto en japonés:** usa splitting basado en caracteres, no en palabras (el japonés no tiene espacios)

## Fuentes

- [Chunking Strategies for LLM Applications (Pinecone)](https://www.pinecone.io/learn/chunking-strategies/)
- [LangChain — Text Splitters](https://js.langchain.com/docs/modules/data_connection/document_transformers/)
- [Evaluating Chunking Strategies (Unstructured.io)](https://unstructured.io/blog/chunking-for-rag-best-practices)
