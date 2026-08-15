# One-Page Cheat Sheet

# Developer-Side Memory Optimization for Reducing Token Cost

## Core Frame

We are developers building a website, chatbot, support system, or internal tool using an LLM API.

The goal is not to optimize the provider's backend. The goal is to design our application so it sends fewer unnecessary tokens and avoids unnecessary LLM calls.

Main question:

> Before calling the LLM API, does the model need this information right now?

If not, choose one:

Remove, summarize, retrieve, cache, limit, batch, or route.

## The Six Techniques

| Technique | What It Means | Use When | How It Reduces Cost | Main Risk |
|---|---|---|---|---|
| Prompt Pruning | Remove repeated, vague, or unnecessary prompt text. | Prompt is long, messy, or copied from experiments. | Fewer input tokens. | Removing useful context can reduce quality. |
| Conversation Summary | Replace old chat history with a compact summary plus recent turns. | Long chat sessions. | Sends summary instead of full history. | Summary can miss or distort details. |
| Retrieval/RAG | Store docs outside the prompt and retrieve only relevant chunks. | PDFs, policies, knowledge bases, manuals. | Sends only useful document parts. | Bad retrieval means bad answers. |
| Response/Semantic Caching | Save previous answers and reuse them for same or similar questions. | Repeated FAQs or stable support answers. | Can skip the LLM call completely. | Cached answers can become stale or unsafe. |
| Output Control | Control answer length and format. | Answers are too long or internal workflows need structured output. | Fewer output tokens and lower latency. | Too-short answers can be incomplete. |
| Batch + Model Routing | Move non-urgent jobs to background; choose model by task difficulty. | Bulk work, reports, classification, mixed workloads. | Cheaper async processing, fewer duplicate calls, cheaper models for simple tasks. | Wrong routing can reduce quality. |

## Best Examples To Say

Prompt pruning:

```text
Before: Be helpful. Be friendly. Be concise. Do not write too much.
After: Answer in simple language using 3 concise bullet points.
```

Conversation summary:

```text
Before: Send all 40 messages.
After: Send summary + last 4-6 messages.
```

RAG:

```text
Without RAG: full PDF + question + answer
With RAG: relevant chunks + question + answer
```

Response caching:

```text
Same FAQ question -> return cached answer -> no LLM call.
```

Output control:

```text
Answer in 4 bullets: cause, next step, warning, contact option.
```

Routing:

```text
Greeting -> no LLM
FAQ -> cache or cheap model
Complex risky task -> strong model or human review
```

## Advanced Names To Mention

| Pattern | Names |
|---|---|
| RAG/vector search | Pinecone, Weaviate, Microsoft Azure AI Search, OpenAI vector stores |
| Semantic caching | Redis, Cloudflare AI Gateway, LangChain |
| Routing/fallback | LiteLLM, LangChain |
| Batch processing | OpenAI Batch API, Claude Message Batches |
| Conversation memory | LangChain |
| Support automation | Zendesk, Intercom |

Use these as examples of real tools/platforms, not as required choices.

## Case Study: Support Bot

Before:

- Strong LLM for every request
- Full policy PDF every request
- Full chat history every request
- Long free-form answers
- Repeated FAQs call the LLM again

After:

- Pruned prompt
- Summary plus recent turns
- RAG retrieves policy chunks
- Cached FAQ answers
- Short structured replies
- Batch jobs and model routing

## Closing Line

Memory optimization is application design: keep the right information in the cheapest useful place, and avoid the LLM call when the app already knows the answer.
