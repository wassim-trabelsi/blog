---
date: 2026-03-07
authors:
  - wassim-trabelsi
categories:
  - Production AI
  - LLM
tags:
  - frameworks
  - production-tips
  - debugging
  - engineering
comments: true
description: AI frameworks abstract the wrong layer. In production, you need full control over your context.
---

# Why You Should Avoid AI Frameworks

LangChain, LlamaIndex, Haystack, CrewAI, DSPy. The AI ecosystem is drowning in orchestration frameworks. They promise rapid prototyping, clean abstractions, and "production-ready" pipelines.

I've used them. I've shipped with them. And I've ripped them out.

Not because they're bad software. Because they abstract the wrong layer.

## The Wrong Abstraction

Here's what matters in production LLM systems:

1. **What goes into the prompt** (your context)
2. **How documents are formatted** (boundaries, IDs, separators)
3. **What the model actually receives** (the full payload)
4. **What comes back** (parsing, validation, error handling)

These four things are where 90% of your bugs live. Citation hallucination? That's #2. Token budget blowup? That's #1. Unparseable responses? That's #4.

AI frameworks abstract exactly these four things away from you.

They give you a nice `.invoke()` or `.run()` method, and somewhere deep inside, they build a prompt, format your documents, add system instructions, maybe inject few-shot examples, and fire it off to the API. You don't see any of it.

When it works, it's magic. When it breaks, you're blind.

## The Debugging Nightmare

Here's a scenario that happens more often than it should.

You have a RAG pipeline in production. Users report wrong answers. You look at the logs. The retrieval step returned the right documents. The model's response looks reasonable. But the citations don't match the content.

You need to see the actual prompt that was sent to the model. The full text. The exact token sequence.

With a thin wrapper around the API, you `print(prompt)` and you're done. Five seconds.

With a framework, you start a journey. You dig into the chain definition. You find a `PromptTemplate` that references a `Retriever` that feeds into a `StuffDocumentsChain` that wraps a `LLMChain`. Each layer adds its own formatting. The actual prompt is assembled at runtime, deep inside the framework's call stack.

You can't just print it. You have to either:

- Add a callback handler and intercept the API call
- Use a proxy like mitmproxy to sniff the HTTP request
- Read the framework's source code to understand what it constructs

Hamel Husain wrote the definitive piece on this: [Fuck You, Show Me The Prompt](https://hamel.dev/blog/posts/prompt/). He uses mitmproxy to intercept what frameworks actually send to the API. The results are revealing: unnecessary system messages, bloated prompts, redundant API calls, and formatting that you'd never write yourself.

The time you saved by not writing a prompt template, you spent tenfold debugging the framework's prompt template.

## What Frameworks Get Wrong

**They hide the prompt.** The prompt is your product. It's the single most important artifact in your system. Hiding it behind abstractions is like hiding your SQL queries behind an ORM and wondering why your database is slow. Sometimes you need the ORM. But you always need to be able to see the query.

**They add coupling.** Your pipeline depends on the framework's release cycle, its breaking changes, its opinions about how documents should be formatted. When they change how `StuffDocumentsChain` concatenates text, your citation validation breaks and you don't know why.

**They make observability harder.** Good production systems log every prompt and every response. With raw API calls, this is trivial. With frameworks, you need to hook into their callback system, which may or may not expose what you need.

**They optimize for demos, not debugging.** A framework that makes a 5-line demo possible but makes a 5-minute debug session take 2 hours has the wrong priorities for production.

## What You Should Do Instead

Build thin utility functions that you own and understand completely.

```python
def build_prompt(query: str, docs: list[str], system: str) -> list[dict]:
    """You can see exactly what goes to the model."""
    formatted_docs, id_map = format_docs_for_prompt(docs)

    return [
        {"role": "system", "content": system},
        {"role": "user", "content": f"{formatted_docs}\n\nQuestion: {query}"},
    ], id_map


def call_llm(messages: list[dict], model: str = "gpt-4") -> str:
    """Thin wrapper. Log everything. No magic."""
    logger.info(f"Prompt: {messages}")
    response = client.chat.completions.create(
        model=model,
        messages=messages,
    )
    result = response.choices[0].message.content
    logger.info(f"Response: {result}")
    return result
```

This is ~20 lines of code. You own it. You can read it. You can debug it. You can log it. You can change how documents are formatted without reading 500 lines of framework source.

The `format_docs_for_prompt` function from my [citation hallucination post](avoid-citation-hallucination-rag.md) is exactly this kind of utility: small, clear, does one thing, and you control the output completely.

## When Frameworks Are Okay

I'm not saying never use them.

**Use them for:**

- Prototyping and exploration get something working in 10 minutes
- Learning see how different patterns are structured
- Hackathons ship fast, throw away later

**Don't use them for:**

- Production systems where you need to debug at 2am
- Pipelines where prompt quality directly impacts revenue
- Any system where you need to explain to your team what the model actually receives

The rule is simple: if you can't `print(prompt)` in one line, you've lost control of your system.

## The Bottom Line

The AI frameworks market is a solution in search of a problem. The "problem" they solve calling an API and formatting a prompt is not hard. It's 20 lines of Python. What IS hard is making that prompt correct, debugging it when it's wrong, and observing it in production.

Frameworks make the easy part easier and the hard part harder. That's the wrong tradeoff.

Own your context. Own your prompts. Own your pipeline. The abstractions you need are the ones you build yourself.

---

*This post connects to [Stop Citation Hallucination in RAG](avoid-citation-hallucination-rag.md) a concrete example of what happens when you don't control how your documents are formatted in the prompt.*

Have thoughts on this? [Reach out on LinkedIn](https://www.linkedin.com/in/wassim-trabelsi-3490aa11a).
