# Same-Day Study Schedule

Date: August 14, 2026
Speech: August 15, 2026
Goal: deliver a clear beginner-friendly developer webinar.

## Rule For Today

Do not study provider internals deeply. Focus on what a developer controls when building an API-based chatbot or system.

Core line to remember:

> Memory optimization is application design: send less unnecessary context and avoid unnecessary LLM calls.

## 6-Hour Preparation Plan

### Hour 1: Frame And Basics

Study:

- We are building an app using LLM APIs.
- Input tokens are what our app sends.
- Output tokens are what the model generates.
- Every LLM API call can cost money.
- Long history, full documents, all tools, and long answers increase cost.

Practice saying:

> Cost opportunity comes from fewer input tokens, fewer output tokens, and fewer API calls.

Done when:

- You can explain the developer-side frame in 60 seconds.
- You can explain input token, output token, and API call cost.

### Hours 2-3: Six Techniques

For each technique, memorize:

1. What is it?
2. When do developers use it?
3. How does it reduce cost?
4. One example
5. One risk

Techniques:

- Prompt pruning
- Conversation summary
- Retrieval/RAG
- Response caching / semantic caching
- Output control / output budgeting
- Batch processing and model routing

Done when:

- You can explain each technique in about 2 minutes.
- You can give the support chatbot example for each technique.

### Hour 4: Advanced Names

Learn names only as examples, not deep tutorials:

- RAG/vector search: Pinecone, Weaviate, Microsoft Azure AI Search, OpenAI vector stores
- Semantic caching: Redis, Cloudflare AI Gateway, LangChain
- Routing/fallback: LiteLLM, LangChain
- Batch processing: OpenAI Batch API, Claude Message Batches
- Conversation memory: LangChain
- Support automation: Zendesk, Intercom

Done when:

- You can say what each group is used for in one sentence.

### Hour 5: Case Study Practice

Practice this story:

We build a customer-support chatbot. At first, every question calls a strong LLM, sends the full policy PDF, sends full chat history, and produces long answers.

Then we optimize it:

1. Prune the prompt.
2. Summarize old chat history.
3. Use RAG to retrieve policy chunks.
4. Cache repeated FAQ answers.
5. Control output length and format.
6. Batch offline jobs and route simple tasks to cheaper paths.

Done when:

- You can tell the story naturally in 5-7 minutes.
- You can explain that RAG still uses the LLM but sends fewer document tokens.
- You can explain that response caching can skip the LLM call completely.

### Hour 6: Full Rehearsal

Do one complete run:

- Open `slides.html`.
- Press `N` if you want speaker notes.
- Use right/left arrow keys to navigate.
- Speak out loud.
- Use a timer.

Target:

- Minimum: 25 minutes
- Ideal: 32-36 minutes
- Maximum: 40 minutes

After rehearsal, fix only major issues:

- A confusing slide
- A missing example
- A section that takes too long

Do not rewrite everything tonight.

## Night-Before Review

Read only:

1. `cheat_sheet.md`
2. Slide titles
3. Opening script
4. Closing framework

Avoid pricing details at night. Pricing changes often, so speak generally unless you checked the official page immediately before the talk.

## Morning Review

Spend 20 minutes:

- Read the cheat sheet once.
- Practice the opening.
- Practice the RAG clarification.
- Practice the response caching explanation.
- Practice the case study.
- Practice the closing line.

Final confidence line:

> I do not need to know every tool deeply. I need to clearly teach the patterns developers use to reduce tokens and API calls.
