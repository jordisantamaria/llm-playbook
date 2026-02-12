# Build vs Buy

## API directa vs Plataformas

### API directa (Claude API, OpenAI API)

Llamas a la API directamente desde tu código.

**Pros:**
- Control total sobre prompts, modelos, pipeline
- Coste más bajo (sin margen de intermediario)
- Features específicas del proveedor (prompt caching de Claude, extended thinking)

**Contras:**
- Tú construyes y mantienes todo
- Necesitas gestionar rate limiting, reintentos, failover por tu cuenta

**Ideal para:** aplicaciones custom, startups, cuando necesitas control total

### Azure OpenAI

Modelos de OpenAI alojados en infraestructura de Azure.

**Pros:**
- Compliance empresarial (residencia de datos, SLAs)
- Clientes enterprise japoneses a menudo requieren Azure
- Misma API que OpenAI, fácil de migrar
- Integración con VNet, private endpoints

**Contras:**
- Ligeramente por detrás en los últimos modelos (delay en el deployment)
- Complejidad de suscripción de Azure
- Coste más alto que la API directa

**Ideal para:** clientes enterprise japoneses con contratos Azure existentes

### AWS Bedrock

Múltiples modelos (Claude, Llama, Mistral) a través de AWS.

**Pros:**
- Todos los modelos a través de una sola API de AWS
- Los datos se quedan en AWS (importante para compliance)
- Funciona con infraestructura AWS existente (IAM, VPC, CloudWatch)
- Claude está disponible en Bedrock

**Contras:**
- No siempre las últimas versiones de los modelos de forma inmediata
- El pricing de AWS es por token, puede ser más alto
- Otro servicio de AWS que gestionar

**Ideal para:** empresas muy invertidas en AWS

### Google Vertex AI

Gemini y otros modelos a través de Google Cloud.

**Pros:**
- Modelos Gemini, integración con Google Search
- Bueno si el cliente usa Google Cloud
- Grounding con Google Search

**Contras:**
- Selección de modelos más pequeña que Bedrock
- Menos adopción enterprise en Japón comparado con Azure/AWS

## Framework de decisión para clientes

```
Client has Azure contract?
  → Azure OpenAI (they'll want this for compliance)

Client has AWS infrastructure?
  → AWS Bedrock (keep everything in one cloud)

Client is a startup / small team?
  → Direct API (fastest to build, cheapest)

Client needs multiple models?
  → AWS Bedrock or direct APIs with Vercel AI SDK

Client has strict data residency requirements?
  → Azure OpenAI or AWS Bedrock (private endpoints)
```

## Ángulo de consultoría

En Japón, la pregunta build-vs-buy a menudo se responde según el proveedor de cloud existente del cliente:
- **SIer / Enterprise tradicional:** Azure (relación con Microsoft)
- **Empresas web:** AWS
- **Startups:** API directa

Conoce las tres opciones para poder asesorar. El cliente valora a alguien que dice "basándonos en tu infraestructura, te recomiendo X" frente a "solo sé usar la API directa."

## Fuentes

- [Azure OpenAI Service](https://azure.microsoft.com/en-us/products/ai-services/openai-service)
- [AWS Bedrock](https://aws.amazon.com/bedrock/)
- [Google Vertex AI](https://cloud.google.com/vertex-ai)
- [Anthropic — Claude on AWS](https://www.anthropic.com/claude/aws-bedrock)
