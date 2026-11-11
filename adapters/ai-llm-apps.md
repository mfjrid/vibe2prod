# AI & LLM Applications Adapter

Audit guidance for web applications integrating Large Language Models (LLMs), AI agents, embeddings, and vector search (e.g., OpenAI, Anthropic, Gemini, LangChain, LlamaIndex, Pinecone, pgvector).

AI-powered features built through rapid prototyping and vibe-coding carry unique vulnerabilities: catastrophic cost runaway, client-side secret exposure, prompt injection, and cross-tenant data leakage in RAG pipelines.

## Primary Inspection Targets

1. **API Keys & Model Configuration**:
   - Provider SDK imports (`openai`, `@anthropic-ai/sdk`, `@google/genai`, `langchain`).
   - Where keys are loaded and whether they are exposed to client-side bundles.
   - Model parameters: `model`, `temperature`, `max_tokens` / `max_completion_tokens`.

2. **Endpoints & Rate Limiting**:
   - Routes that invoke LLMs (e.g., `/api/chat`, `/api/generate`, Server Actions).
   - Rate limiting, token quotas, and abuse prevention mechanisms.

3. **Prompt Construction & Input Handling**:
   - System prompts, template strings, user input concatenation.
   - Guardrails against prompt injection and jailbreaks.

4. **RAG & Vector Search**:
   - Embedding generation and vector queries (`supabase/pgvector`, `pinecone`, `qdrant`, `weaviate`).
   - Multi-tenant isolation in similarity search filters.

5. **Streaming & Serverless Execution**:
   - Vercel AI SDK (`ai`), server-sent events (SSE), or WebSockets.
   - Connection handling, client disconnect cancellations, and timeout configurations.

---

## Critical Audit Checklist

### 1. Cost Control & API Abuse Prevention (P0 / P1)
- [ ] **No Client-Side Provider Calls**: Confirm LLM provider SDKs and API keys are NEVER initialized in browser code. All AI calls must terminate at a backend/serverless proxy.
- [ ] **Strict Per-User Rate Limiting**: AI endpoints are expensive. Verify strict rate limits exist per authenticated user ID and per IP address (e.g., max 10 requests/minute).
- [ ] **Token Limits Enforced**: Every model call MUST specify `max_tokens` (or `max_completion_tokens`). Unbounded token generation allows attackers to cause severe financial denial-of-wallet (DoW).
- [ ] **Daily / Monthly Usage Quotas**: Check whether users have a hard credit/usage cap to prevent automated bill exhaustion.

### 2. Prompt Injection & Input Validation
- [ ] **Clear Separation of System & User Roles**: Ensure untrusted user content is passed in the `user` role payload, never interpolated directly into the `system` instructions.
- [ ] **Input Length Capping**: Enforce maximum character/token limits on user prompts before sending them to the model.
- [ ] **Indirect Prompt Injection**: If the app summarizes external URLs, emails, or uploaded documents, verify the prompt warns the model not to execute instructions found in external data.

### 3. Structured Outputs & Hallucination Defense
- [ ] **Schema Enforcement**: If the application expects JSON from the LLM, verify it uses provider-native Structured Outputs (e.g., OpenAI `response_format: { type: "json_schema" }`, Gemini structured outputs, or Instructor/Zod/Pydantic validation).
- [ ] **Parse Error Fallbacks**: Verify the code handles JSON parse failures gracefully without crashing or throwing unhandled 500 errors.

### 4. RAG & Vector Database Tenant Isolation
- [ ] **Multi-Tenant Filter in Vector Queries**: In multi-tenant or multi-user applications, similarity searches MUST include strict metadata filters (e.g., `WHERE tenant_id = current_tenant` or `metadata: { user_id: auth.uid() }`). Vector similarity alone does NOT enforce data privacy.
- [ ] **Document Ingestion RLS**: Confirm uploaded documents and knowledge bases verify read/write authorization before embedding and storing.

### 5. Streaming, Timeouts & Abort Controllers
- [ ] **Cancellation on Disconnect**: When streaming responses to the frontend, verify the backend listens to `AbortSignal` / request cancellation. If the user closes the tab, the backend must abort the LLM stream immediately to stop consuming billable tokens.
- [ ] **Timeout Safeguards**: Long-running generation must have a reasonable timeout (e.g., 30-60 seconds) to avoid hanging serverless functions or worker threads.

### 6. Logging, Privacy & Secrets
- [ ] **Redact PII & Secrets from Prompts**: Sensitive user data (passwords, payment details) must be stripped before sending to external LLM providers.
- [ ] **Provider Data Privacy**: Note whether provider enterprise terms (e.g. zero data retention) are required for regulatory compliance (GDPR, HIPAA).

