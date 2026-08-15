# Speaker Notes: Memory Optimization Techniques for Reducing Token Cost

Target length: 30-40 minutes
Audience: beginner developers
Speech date: August 15, 2026

## Opening Script

Good morning/afternoon everyone. Today I will talk about memory optimization techniques for reducing token cost in API-based LLM applications.

The focus is from a developer's perspective. Imagine we are building a website, chatbot, support system, or internal tool using an API from OpenAI, Claude, DeepSeek, or another LLM provider.

Many of us know that tokens are the units used by language models to process text and calculate cost. But the real cost problem usually starts when our application sends too much memory to the model: full chat history, full documents, repeated instructions, unnecessary tool data, and long answers.

So the main idea today is simple:

> Memory is useful, but memory is not free.

In this session, I will explain six practical developer-side techniques: prompt pruning, conversation summary, retrieval or RAG, response caching, output control, and batch processing with model routing.

## Slide 2: Why Token Cost Grows

Explain this with a support chatbot example.

A simple support bot might start with:

- System instruction
- User question
- Short model answer

That is cheap. But after some time, the app may send:

- The full system prompt
- The full policy document
- The full chat history
- All available tools
- A request for a long answer

Even if the user asks only one small question, the model still receives a large input. That means higher cost and often higher latency.

Key sentence:

> The model does not know what is important unless our application chooses what to send.

## Slide 3: Token Cost Basics

Definitions to say clearly:

- A token is a small unit of text used by the model.
- Input tokens are what we send to the model.
- Output tokens are what the model generates.
- Context window is the maximum amount of information a model can consider in one request.

Important explanation:

Input tokens include more than the user's message. They can include system prompts, developer instructions, previous conversation, retrieved documents, tool definitions, function schemas, examples, and formatting rules.

Output tokens also matter. If we ask for a long essay when a short answer is enough, we pay more and wait longer.

Transition:

> So optimization means controlling both sides: what goes in and what comes out.

## Slide 4: Memory Mental Model

Use four memory types:

1. Prompt memory
   This is what we send directly in the current request. It is simple but can become expensive.

2. Summary memory
   This compresses old conversation into a shorter form.

3. Retrieval memory
   This stores knowledge outside the prompt and brings only relevant parts into the request.

4. Cached answer memory
   This stores previous answers in our own application so repeated or similar questions may not need another LLM call.

Key sentence:

> The cheapest memory is the memory we do not send. The next cheapest is memory we compress, retrieve, or answer from our own cache.

## Technique 1: Prompt Pruning

What is it?

Prompt pruning means removing unnecessary text from the prompt. This can include repeated instructions, irrelevant examples, old conversation, unused context, or verbose wording.

Simple before/after example:

Before pruning:

```text
You are a helpful assistant. Please answer clearly. Make sure your answer is useful.
Use simple language. Do not be too long. Be concise. The user may be confused,
so explain in a friendly way. Give the final answer in bullet points if possible.
```

After pruning:

```text
Answer in simple language using 3 concise bullet points.
```

Both prompts ask for almost the same behavior, but the second one uses fewer tokens and is easier for the model to follow.

When is it used?

Use it when the prompt is long, copied from many experiments, or includes content that is not needed for the current task.

How is pruning done?

There are two common ways: manual pruning and automatic pruning.

Manual pruning:

- Remove repeated instructions.

  Example:

  ```text
  Before: Be short. Be concise. Do not write too much. Avoid long answers.
  After: Keep the answer under 5 bullet points.
  ```

  Name to mention: OpenAI, Claude, DeepSeek

- Replace long explanations with one clear instruction.

  Example:

  ```text
  Before: The user may not understand technical words, so explain everything
  in a friendly way and avoid complicated terminology whenever possible.

  After: Explain in beginner-friendly language.
  ```

  Name to mention: OpenAI, Claude, DeepSeek

- Delete examples that are not needed for the current task.

  Example:

  ```text
  Before: Include 5 sample answers for refund, login, delivery, invoice,
  and cancellation, even when the user asks only about login.

  After: Include only the login example.
  ```

  Name to mention: OpenAI, Claude, DeepSeek

- Keep only the latest or most relevant conversation turns.

  Example:

  ```text
  Before: Send all 30 previous messages.
  After: Send the last 6 messages plus important user facts.
  ```

  Name to mention: LangChain

- Remove formatting rules that do not affect the answer.

  Example:

  ```text
  Before: Use bullets. Use markdown. Use short lines. Use headings.
  Make the answer visually clear. Avoid long paragraphs.

  After: Use short markdown bullets.
  ```

  Name to mention: OpenAI, Claude, DeepSeek

- Combine similar rules into one rule.

  Example:

  ```text
  Before: Be polite. Be friendly. Be respectful. Be professional.
  After: Use a polite professional tone.
  ```

  Name to mention: OpenAI, Claude, DeepSeek

Automatic pruning:

- Use code to keep only the last N conversation messages.

  Example:

  ```text
  messages_to_send = system_prompt + chat_history[-6:] + current_question
  ```

  Name to mention: LangChain

- Drop old messages after a token limit is reached.

  Example:

  ```text
  while token_count(messages) > 3000:
    remove_oldest_message(messages)
  ```

  Name to mention: LangChain

- Use a tokenizer to measure prompt size before sending.

  Example:

  ```text
  if token_count(system_prompt + history + question) > max_budget:
    trim_history()
  ```

  Name to mention: OpenAI, Anthropic

- Use a smaller model to classify which context is relevant.

  Example:

  ```text
  label = cheap_model("Does this question need refund_policy, login_policy, or no document?")
  send_only(document_for(label))
  ```

  Name to mention: LangChain

- Use retrieval search to select only relevant chunks.

  Example:

  ```text
  chunks = vector_search(question, top_k=3)
  prompt = system_prompt + chunks + question
  ```

  Name to mention: OpenAI, Microsoft Azure

- Use a summarizer to compress old context instead of keeping it all.

  Example:

  ```text
  if chat_history_tokens > 3000:
    summary = summarize(old_messages)
    prompt = summary + last_6_messages + current_question
  ```

  Name to mention: LangChain

For beginners, start with manual pruning first. Automatic pruning becomes useful when the application has many users, long conversations, or large documents.

How does it reduce cost?

Every unnecessary input token removed is a token we do not pay for again.

Pros:

- Very simple
- Works with all providers
- No extra infrastructure needed
- Improves clarity

Cons:

- If we remove useful context, answer quality can drop
- Needs human judgment or testing

Example:

Instead of sending a long writing guide, send:

```text
Answer in 3 bullet points.
Use simple language.
Mention one warning if needed.
```

Key sentence:

> First remove noise before adding advanced architecture.

## Technique 2: Conversation Summary

What is it?

Conversation summary means replacing old chat history with a compact summary of important facts, decisions, and user preferences.

Simple before/after example:

Before summary:

```text
User: I forgot my password.
Assistant: Try the reset link.
User: I did not get the email.
Assistant: Check spam or junk folder.
User: I use the mobile app.
Assistant: Open account settings from the app.
... 30 more messages ...
Current question: What should I try next?
```

After summary:

```text
Summary:
User is trying to reset a business account password.
The reset email did not arrive.
User is using the mobile app.
User prefers short step-by-step instructions.

Recent messages:
Keep only the last 4-6 turns.

Current question:
What should I try next?
```

Both versions give context, but the second version sends fewer tokens and keeps the important memory.

When is it used?

Use it in long chatbots, tutoring apps, support systems, or assistant applications where conversations continue over many turns.

How is conversation summary done?

There are several common ways to create and maintain summary memory.

- Manual summary.

  Example:

  ```text
  Human support agent writes:
  User cannot access business account. Password reset email failed.
  User is on mobile app. Next step: verify email address and resend link.
  ```

  Name to mention: customer support teams, tutoring teams

- Automatic summary after every few messages.

  Example:

  ```text
  every 5 messages:
    summary = summarize(previous_summary + latest_messages)
    clear_old_messages()
  ```

  Name to mention: LangChain

- Summary only when token limit is crossed.

  Example:

  ```text
  if token_count(chat_history) > 3000:
    summary = summarize(old_messages)
    prompt = summary + last_6_messages + current_question
  ```

  Name to mention: LangChain, Claude

- Structured summary.

  Example:

  ```text
  User goal: reset business account password
  Known facts: reset email did not arrive; user is on mobile app
  Decisions: use short step-by-step instructions
  Open issue: verify account email address
  ```

  Name to mention: OpenAI, Claude, DeepSeek

- Rolling summary that updates over time.

  Example:

  ```text
  new_summary = update_summary(old_summary, latest_conversation)
  send(new_summary + recent_messages + current_question)
  ```

  Name to mention: LangChain

Important warning:

A summary is a compressed memory, not perfect memory. If the summary misses a detail or stores a wrong fact, future answers can become wrong. For important information, keep the exact fact separately, such as account type, user preference, deadline, or decision.

How does it reduce cost?

Instead of sending 40 previous messages, we send the latest few turns plus a short summary.

Pros:

- Keeps continuity
- Reduces repeated history
- Works with OpenAI, Claude, DeepSeek, and most LLM APIs

Cons:

- The summary may miss details
- A wrong summary can carry wrong information forward
- Needs periodic refresh or validation

Example summary:

```text
User is trying to reset a business account password.
They already tried email reset.
They use the mobile app and cannot access the admin panel.
They prefer short step-by-step instructions.
```

Key sentence:

> A summary is not perfect memory, but it is usually cheaper than sending the whole conversation.

## Technique 3: Retrieval/RAG

What is it?

Retrieval-Augmented Generation, or RAG, stores documents outside the prompt and retrieves only the most relevant chunks for the current question.

Important clarification:

RAG does not mean the LLM becomes free. The LLM is still used to write the final answer. RAG reduces cost because we avoid sending the whole document or knowledge base to the LLM every time.

Simple cost idea:

```text
Without RAG:
Send full PDF + user question + ask LLM to answer.

With RAG:
Search the PDF first.
Send only 2-3 relevant paragraphs + user question + ask LLM to answer.
```

So yes, the LLM still takes cost. But the input is much smaller.

Traditional file upload vs custom RAG:

Traditional file upload:

```text
User uploads a PDF to a platform.
The platform may handle document reading internally.
We usually do not control chunk size, ranking, search logic, or exactly what text is sent.
```

Custom RAG chatbot:

```text
User question
-> search our PDF/doc/txt database
-> select relevant chunks
-> send chunks + question to the LLM
-> LLM writes the final answer
```

For this webinar, we are mainly talking about custom chat websites, custom chatbots, and developer-built applications where we control the retrieval step.

When is it used?

Use it for large documents, company policies, knowledge bases, PDFs, product manuals, and frequently updated information.

How does it reduce cost?

The app avoids sending the entire document. It sends only the relevant chunks, so the main saving is in input tokens.

Cost comparison:

```text
Without RAG:
cost = full_document_tokens + question_tokens + answer_tokens

With RAG:
cost = search_cost + relevant_chunk_tokens + question_tokens + answer_tokens
```

The search step also has some cost, but it is usually much smaller than sending a large document again and again.

Pros:

- Scales better than pasting full documents
- Keeps knowledge outside the prompt
- Can update documents without changing the main prompt

Cons:

- Needs chunking and search setup
- Bad retrieval means bad answers
- Requires evaluation
- Saves tokens only if the retrieved chunks are actually relevant

Provider note:

OpenAI has official retrieval and vector store examples. But the RAG pattern is general and can be used with Claude, DeepSeek, or other providers.

Example:

Bad approach:

```text
Send the entire 80-page refund policy every time.
```

Better approach:

```text
Search the policy.
Retrieve the top 3 relevant chunks.
Send those chunks with the user question.
```

Key sentence:

> Retrieval changes the problem from "send everything" to "find the right few pieces."

Second key sentence:

> RAG does not make the LLM free. It makes the LLM read only the useful part instead of the whole library.

## Technique 4: Response Caching / Semantic Caching

What is it?

Response caching means saving an answer from a previous LLM call and reusing it when the same question is asked again.

Semantic caching means reusing a saved answer when a new question has almost the same meaning, even if the wording is different.

Simple example:

```text
First user:
What is your refund policy?

App:
Call LLM -> generate answer -> save question + answer in cache.

Second user:
Can I get my money back after buying?

App:
Find similar cached question -> return saved answer -> no LLM call.
```

This is developer-side caching. We build it in our own application using a database, Redis, vector search, or an FAQ table.

When is it used?

Use it when many users ask the same or similar questions:

- FAQ questions
- Product policy questions
- Support chatbot questions
- University admission rules
- Pricing or plan questions
- Repeated troubleshooting steps

How does it reduce cost?

It can avoid the LLM API call completely.

Cost idea:

```text
Without response cache:
same question -> call LLM -> pay input tokens + output tokens

With response cache:
same question -> return saved answer -> no LLM token cost
```

Types of caching:

- Exact question cache.

  Example:

  ```text
  cache_key = lowercase(user_question)
  if cache_key exists:
    return cached_answer
  ```

  Name to mention: Redis, database cache

- FAQ cache.

  Example:

  ```text
  if question matches "refund policy":
    return approved FAQ answer
  ```

  Name to mention: Intercom, Zendesk

- Semantic cache.

  Example:

  ```text
  embedding = create_embedding(user_question)
  similar_answer = vector_search(embedding)
  if similarity > 0.90:
    return cached_answer
  else:
    call_llm()
  ```

  Name to mention: LangChain, Redis, vector database

- Time-based cache.

  Example:

  ```text
  Cache answer for 24 hours.
  After 24 hours, call the LLM again and refresh the answer.
  ```

  Name to mention: Redis, Cloudflare

Pros:

- Can reduce cost to zero for repeated questions
- Improves response speed
- Works with any LLM provider
- Very useful for FAQ/support systems

Cons:

- Cached answers can become outdated
- Semantic cache can return a wrong answer if similarity matching is too loose
- Not suitable for highly personal or sensitive answers without careful rules
- Needs cache invalidation when policies or documents change

Important warning:

Do not cache every answer blindly. Cache answers for stable, repeatable questions. Avoid caching private user-specific answers unless your system has strict user/session separation.

Key sentence:

> Response caching reduces token cost because sometimes we skip the LLM call completely.

## Technique 5: Output Control / Output Budgeting

What is it?

Output control means deciding how much the model should write and what format the answer should follow.

In an API-based system, developers can control output using:

- Output token limits
- Clear length instructions
- Bullet-point formats
- JSON or structured responses
- Short answer vs detailed answer modes
- Templates for common tasks

When is it used?

Use it when answers are longer than needed, when users need fast answers, or when internal workflows only need short structured output.

How does it reduce cost?

Output tokens are generated by the model and billed by the provider. Shorter useful responses mean fewer output tokens, lower cost, and often lower latency.

Simple API idea:

```text
max_output_tokens = 150
```

Simple prompt idea:

```text
Answer in 4 bullets:
- likely cause
- next step
- warning
- when to contact support
```

Developer techniques:

- Set an output token limit.

  Example:

  ```text
  max_output_tokens = 150
  ```

  Name to mention: OpenAI, Claude, DeepSeek

- Ask for bullets instead of long paragraphs.

  Example:

  ```text
  Answer in 5 short bullet points.
  ```

  Name to mention: OpenAI, Claude, DeepSeek

- Use structured JSON for internal workflows.

  Example:

  ```json
  {
    "category": "billing",
    "priority": "high",
    "next_action": "send_to_agent"
  }
  ```

  Name to mention: OpenAI, Claude

- Create response modes.

  Example:

  ```text
  If user selects "quick answer", respond in 3 bullets.
  If user selects "detailed answer", respond with explanation and examples.
  ```

  Name to mention: custom chatbot systems

- Use templates for repeated answer types.

  Example:

  ```text
  Password reset answer format:
  1. What happened
  2. What to try now
  3. What to do if it fails
  ```

  Name to mention: support automation systems

Pros:

- Simple
- Works everywhere
- Often improves user experience
- Helpful for APIs and automation
- Reduces latency as well as token cost

Cons:

- If too strict, the answer may miss important detail
- Some tasks need longer reasoning or explanation
- Structured output can be harder for normal users to read
- Very low token limits can cut off the answer

Examples:

Instead of:

```text
Explain everything about this error.
```

Use:

```text
Answer in 5 bullets:
1. likely cause
2. quick fix
3. long-term fix
4. risk
5. next step
```

Key sentence:

> The target is not the shortest answer. The target is the shortest useful answer.

## Technique 6: Batch Processing and Model Routing

What is batch processing?

Batch processing means moving non-urgent LLM work to a background job instead of doing it immediately while the user is waiting.

In a custom API-based system, the developer decides:

- Which tasks need real-time answers
- Which tasks can run later
- Which tasks can be grouped together
- Which tasks can be processed during low-traffic time

Use cases:

- Classifying thousands of tickets
- Running evaluations
- Summarizing old records
- Generating embeddings
- Offline analytics
- Creating daily reports
- Moderating old comments

How does batching reduce cost?

Batching can reduce cost in two ways:

1. Some providers offer cheaper batch APIs for asynchronous jobs.
2. Even without a provider discount, batching helps developers avoid unnecessary repeated calls by grouping, deduplicating, and scheduling work.

Developer example:

```text
Bad:
Every time a support ticket arrives, immediately call the LLM to classify it.

Better:
Collect tickets for 10 minutes.
Remove duplicates.
Send classification jobs in one background batch.
```

Provider examples to mention:

- OpenAI Batch API
- Claude Message Batches

Name to mention:

- OpenAI, Claude, background job queues, cron jobs

Tradeoff:

Batch is not suitable when the user needs an immediate answer. It is best for offline work, admin tasks, reports, analytics, and bulk processing.

What is model routing?

Model routing means choosing the model based on task difficulty instead of using the strongest model for everything.

In a custom system, the developer can create a router:

```text
If task is simple classification -> use cheaper/faster model.
If task needs deep reasoning -> use stronger model.
If answer is already cached -> use no model.
If question needs documents -> use RAG first, then call model.
```

Examples:

- Greeting or simple FAQ: return template or cached answer
- Ticket category classification: cheaper model
- Short summary: cheaper/faster model
- Complex reasoning or sensitive answer: stronger model
- Document-heavy answer: retrieval first, then model

How does routing reduce cost?

The application avoids paying for an expensive model when the task does not need it.

Routing example:

```text
User: Hi
App: return normal greeting, no LLM call

User: What is the refund policy?
App: check cache or FAQ first

User: Compare these 3 legal clauses.
App: use stronger model
```

Names to mention:

- OpenAI, Claude, DeepSeek, LangChain

Pros:

- Strong cost leverage
- Good for mixed workloads
- Can improve speed
- Lets developers match model quality to task importance

Cons:

- Needs testing
- Wrong routing can reduce answer quality
- Routing rules can become complex
- Sensitive tasks need careful fallback to stronger models

Important warning:

Do not route only by cost. Route by risk and difficulty. A cheap model is fine for simple classification, but a high-risk answer may need a stronger model or human review.

Key sentence:

> Do not use the strongest model for every task if many tasks are simple, repeated, or offline.

## Slide 11: Case Study

Story:

Imagine we are hired to build a customer-support chatbot for an e-commerce company. The client wants the bot to answer refund, delivery, login, and payment questions using the company's policy documents.

Before optimization:

- Every user question is sent directly to a strong LLM.
- The app sends the full refund policy PDF every time.
- The app sends the full conversation history every time.
- The app sends all available tool/function descriptions every time.
- The model gives long free-form answers.
- The same model is used for greetings, FAQs, summaries, and complex cases.

Developer-side optimization plan:

1. Prompt pruning
   Keep the system prompt short and remove repeated instructions.

   Example:

   ```text
   Before: Be helpful. Be friendly. Be concise. Do not be long.
   After: Answer in a polite tone using short bullet points.
   ```

2. Conversation summary
   Send only the latest few messages plus a summary of older conversation.

   Example:

   ```text
   Summary: User wants refund for order #123. They already checked delivery status.
   Recent messages: last 4-6 turns.
   ```

3. Retrieval/RAG
   Store policies in a database or vector database. Search first, then send only relevant chunks to the LLM.

   Example:

   ```text
   User asks refund question -> retrieve refund policy chunks only.
   User asks login question -> retrieve login guide only.
   ```

4. Response caching
   Save approved answers for common questions.

   Example:

   ```text
   If many users ask "What is the refund policy?",
   return cached FAQ answer without calling the LLM.
   ```

5. Output control
   Set a short response format for normal support answers.

   Example:

   ```text
   Answer in 4 bullets: cause, next step, warning, contact option.
   ```

6. Batch and routing
   Process non-urgent tasks in the background and choose models by task difficulty.

   Example:

   ```text
   Greeting -> no LLM
   FAQ -> cache or cheaper model
   Complex complaint -> stronger model or human review
   Ticket tagging -> background batch job
   ```

Result:

- Fewer input tokens because we do not send the full history or full documents.
- Fewer output tokens because answers are controlled.
- Some questions cost zero LLM tokens because cached answers are returned.
- Offline work can be batched.
- Expensive models are saved for hard or risky tasks.

Main takeaway:

> The user still gets useful support, but our application sends less unnecessary memory and avoids some LLM calls completely.

## Closing Script

To finish, memory optimization in API-based LLM systems is not only prompt engineering. It is application design.

As developers, we decide:

- What context goes into the prompt
- What history should be summarized
- What documents should be retrieved
- What answers can be cached
- What output length is enough
- What work can run later
- Which model should handle each task

Use this decision framework:

- Remove noise
- Compress old history
- Retrieve relevant facts
- Cache repeated answers
- Limit output
- Batch offline work
- Route by difficulty

Some more advanced techniques and names to remember:

- RAG with vector databases: Pinecone, Weaviate, Microsoft Azure AI Search
- Semantic caching: Redis, Cloudflare AI Gateway, LangChain
- LLM routing and fallback: LiteLLM, LangChain
- Batch processing: OpenAI Batch API, Claude Message Batches
- Conversation memory and summarization: LangChain
- Support/FAQ automation patterns: Zendesk, Intercom

I am mentioning these names not because you must use all of them, but because they show that these techniques are used in real developer tools and production-style systems.

Before calling the LLM API, ask:

> Can my application remove this, summarize it, retrieve only the relevant part, return a cached answer, limit the output, batch it, or route it to a cheaper model?

Thank you.

## Likely Q&A

### Is prompt pruning always safe?

No. Remove clearly irrelevant or repeated content first. For important prompts, compare outputs before and after pruning.

### Is RAG always better than long context?

No. RAG is useful when only part of a large knowledge base is needed. If the whole document is genuinely needed, long context may be simpler, but more expensive.

### Does response caching mean the model remembers everything?

No. In our developer-side system, response caching means our app stores a previous answer and may return it later. The model is not remembering it. Our application cache is remembering it.

### When should I not use response caching?

Do not blindly cache private, user-specific, sensitive, or fast-changing answers. Caching is safest for stable FAQ-style questions, approved policy answers, and repeated troubleshooting steps.

### Which technique should I try first?

Start with prompt pruning and output control because they are simple. Then add conversation summary for long chats, RAG for large documents, response caching for repeated questions, and batch/model routing when your system has scale.

### Are OpenAI, Claude, and DeepSeek the main topic?

No. The main topic is developer-side application design. OpenAI, Claude, DeepSeek, and other providers are just examples of APIs our system may call.

### Do advanced tools replace these basic techniques?

No. Tools like Redis, Pinecone, Weaviate, Cloudflare AI Gateway, LangChain, and LiteLLM help implement these patterns, but the developer still has to design the memory strategy.

## Sources To Mention Briefly

- Redis docs: semantic caching for LLM responses.
- Cloudflare AI Gateway docs: response caching and AI gateway controls.
- Pinecone and Weaviate docs: RAG and vector database workflows.
- LangChain docs: memory, summarization, caching, and routing patterns.
- LiteLLM docs: routing, load balancing, and fallback patterns.
- OpenAI and Claude docs: batch APIs and model API usage.
- Zendesk and Intercom docs: customer-support AI agent and automation patterns.
