# LLM Playbook

Personal knowledge base for LLM/AI integration consulting and development.

## Structure

### Fundamentals
- [How LLMs Work](01-fundamentals/how-llms-work.md) — Tokens, context window, temperature, top-p
- [Prompt Engineering](01-fundamentals/prompt-engineering.md) — System prompts, few-shot, chain-of-thought
- [Streaming](01-fundamentals/streaming.md) — SSE, chunked responses, Vercel AI SDK

### APIs
- [Claude API](02-apis/claude-api.md) — Messages API, models, pricing
- [OpenAI API](02-apis/openai-api.md) — Chat completions, models, pricing
- [Structured Output](02-apis/structured-output.md) — JSON mode, Zod schemas, reliable parsing
- [Provider Abstraction](02-apis/provider-abstraction.md) — Vercel AI SDK, swap providers in 1 line

### RAG
- [Embeddings](03-rag/embeddings.md) — What they are, models, dimensions
- [Vector Databases](03-rag/vector-databases.md) — pgvector, Pinecone, comparison
- [Chunking Strategies](03-rag/chunking-strategies.md) — Size, overlap, paragraph vs semantic
- [RAG Pipeline](03-rag/rag-pipeline.md) — Full flow: ingestion -> retrieval -> generation

### Tool Use
- [Function Calling](04-tool-use/function-calling.md) — Define tools, execution, response handling
- [Agents](04-tool-use/agents.md) — Planning, loops, memory, error handling
- [MCP](04-tool-use/mcp.md) — Model Context Protocol (Anthropic)

### Production
- [Cost Optimization](05-production/cost-optimization.md) — Pricing per model, caching, model routing
- [Security](05-production/security.md) — Prompt injection, data leakage, guardrails
- [Observability](05-production/observability.md) — Logging prompts, costs, latency
- [Evaluation](05-production/evaluation.md) — How to test LLM outputs

### Model Selection
- [Model Comparison](06-model-selection/model-comparison.md) — Claude vs GPT vs Gemini
- [Model Routing](06-model-selection/model-routing.md) — Expensive model for complex, cheap for simple
- [When NOT to Use LLM](06-model-selection/when-not-to-use-llm.md) — Cases where rules or classic search wins
- [Build vs Buy](06-model-selection/build-vs-buy.md) — Direct API vs platforms (Bedrock, Azure OpenAI)

### Use Cases
- [Internal Knowledge Base](07-use-cases/internal-knowledge-base.md) — RAG over internal docs, wiki, Confluence
- [Customer Support](07-use-cases/customer-support.md) — Chatbot with FAQ + human escalation
- [Document Processing](07-use-cases/document-processing.md) — Invoices, contracts, OCR + LLM
- [Code Assistant](07-use-cases/code-assistant.md) — Code review, generation, auto-documentation
- [Content Generation](07-use-cases/content-generation.md) — Marketing copy, translations, JP<->EN
- [Data Extraction](07-use-cases/data-extraction.md) — Emails, PDFs, web -> structured data
- [Workflow Automation](07-use-cases/workflow-automation.md) — Multi-step agents for business processes
- [Search Enhancement](07-use-cases/search-enhancement.md) — Semantic search vs keyword in existing apps

### Consulting
- [Discovery Questions](08-consulting/discovery-questions.md) — Questions for the sales meeting
- [Architecture Patterns](08-consulting/architecture-patterns.md) — RAG, agent loop, human-in-the-loop
- [Cost Estimation](08-consulting/cost-estimation.md) — How to estimate API costs for clients
- [ROI Framework](08-consulting/roi-framework.md) — How to justify the investment
- [Proposal Template](08-consulting/proposal-template.md) — Problem -> solution -> timeline -> cost

### Projects
- [ai-chatbot](https://github.com/jordi-santamaria/ai-chatbot) — Streaming chatbot with Vercel AI SDK
- [document-qa](https://github.com/jordi-santamaria/document-qa) — RAG app with pgvector + Claude
- [ai-agent-toolkit](https://github.com/jordi-santamaria/ai-agent-toolkit) — Agent with tool use
