# Sources

These references support the developer-side techniques in the webinar. They are not required reading before the speech; use them if someone asks where the advanced names came from.

Pricing changes often, so avoid exact pricing claims unless you check the official pricing page immediately before presenting.

## Prompt Pruning And Token Counting

- OpenAI token counting with `tiktoken`: https://developers.openai.com/cookbook/examples/how_to_count_tokens_with_tiktoken
- Anthropic token counting: https://platform.claude.com/docs/en/build-with-claude/token-counting
- LangChain short-term memory and trimming concepts: https://docs.langchain.com/oss/python/langchain/short-term-memory

Key points used:

- Developers can measure or estimate prompt size before sending.
- Applications can trim old messages or irrelevant context before an API call.

## Conversation Summary

- LangChain short-term memory: https://docs.langchain.com/oss/python/langchain/short-term-memory
- LangChain conversation memory concepts: https://python.langchain.com/docs/versions/migrating_memory/

Key points used:

- Applications can keep recent messages and summarize older conversation.
- Summary memory is an application pattern, not a special provider-only feature.

## Retrieval/RAG

- Pinecone RAG overview: https://www.pinecone.io/learn/retrieval-augmented-generation/
- Weaviate RAG guide: https://docs.weaviate.io/weaviate/starter-guides/generative
- Microsoft Azure RAG overview: https://learn.microsoft.com/en-us/azure/storage/files/artificial-intelligence/retrieval-augmented-generation/overview
- OpenAI retrieval/vector stores: https://developers.openai.com/api/docs/guides/retrieval

Key points used:

- RAG stores documents outside the prompt and retrieves relevant information before generation.
- RAG still calls the LLM; it reduces cost mainly by reducing document input tokens.

## Response Caching And Semantic Caching

- Redis semantic cache: https://redis.io/docs/latest/develop/use-cases/semantic-cache/
- RedisVL semantic cache: https://redis.io/docs/latest/develop/ai/redisvl/0.7.0/user_guide/llmcache/
- Cloudflare AI Gateway caching: https://developers.cloudflare.com/ai-gateway/features/caching/
- LangChain LLM caching: https://docs.langchain.com/oss/javascript/integrations/llm_caching

Key points used:

- Response caching can return stored answers for repeated requests.
- Semantic caching can reuse an answer for a similar question.
- A cache hit can avoid an LLM call, reducing tokens sent to the provider.

## Output Control

- OpenAI API documentation: https://developers.openai.com/api/docs
- Anthropic Messages API documentation: https://platform.claude.com/docs/en/api/messages
- DeepSeek API documentation: https://api-docs.deepseek.com/

Key points used:

- Developers can control output length and format using API parameters and prompt instructions.
- Structured outputs and concise prompts can reduce generated tokens.

## Batch Processing And Model Routing

- OpenAI Batch API: https://developers.openai.com/api/docs/guides/batch
- Claude Message Batches: https://platform.claude.com/docs/en/build-with-claude/batch-processing
- LiteLLM routing and load balancing: https://docs.litellm.ai/docs/routing-load-balancing
- LiteLLM auto routing: https://docs.litellm.ai/docs/proxy/auto_routing

Key points used:

- Non-urgent LLM work can run asynchronously or in background jobs.
- Applications can route tasks to different models based on cost, quality, risk, and latency.

## Support Automation Examples

- Zendesk AI agents: https://www.zendesk.com/service/ai/ai-agents/
- Intercom Fin AI Agent: https://www.intercom.com/help/en/articles/7120684-fin-ai-agent-explained

Key points used:

- Support systems commonly combine knowledge sources, automation, routing, and AI answers.
- These are examples of real production-style support automation platforms.
