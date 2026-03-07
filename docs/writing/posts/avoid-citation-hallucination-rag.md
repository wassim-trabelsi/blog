---
date: 2025-11-10
authors:
  - wassim-trabelsi
categories:
  - RAG
  - LLM
  - Production AI
tags:
  - rag
  - llm
  - citations
  - debugging
  - production-tips
comments: true
description: Why sequential document IDs break citation validation and how to fix it
---

# Stop Citation Hallucination in RAG

Your RAG system asks the LLM to cite sources. You use DOC 1, DOC 2, DOC 3. The LLM confidently cites DOC 4 which doesn't exist.

This is not a model bug. It's a prompt design bug, and it compounds fast when you build pipelines on top.

## The Problem

**Bad: Sequential numbers**

```
DOC 1: Paris is the capital of France
DOC 2: Python is used for ML
DOC 3: Git enables collaboration
```

The LLM sees `1, 2, 3` and autocompletes the sequence. It hallucinates `DOC 4`, `DOC 5` because that's what autoregressive models do: they predict the next likely token.

**Good: Random letter IDs**

```
DOC [XKJM]: Paris is the capital of France
DOC [PLQW]: Python is used for ML
DOC [BNRT]: Git enables collaboration
```

No pattern to continue. The LLM can't invent `DOC [????]` because there's no sequence to predict. It's forced to either cite an existing ID or cite nothing.

## The Boundary Problem (The Real Mess)

Sequential IDs are only half the problem. The other half is **document boundary confusion**.

Consider a real e-commerce RAG system. You retrieve product pages, FAQ entries, and support tickets. You separate them with `\n`. Sounds fine, until you look at what's actually inside the documents:

```
DOC 1: Nike Air Max 90
Available sizes: 40, 41, 42, 43
Colors: White, Black

Customer reviews:
"Great shoe, very comfortable" - Jean P.
"Runs small, order one size up" - Marie L.

Return policy: 30 days, unworn condition
DOC 2: Adidas Ultraboost 22
Lightweight running shoe with Boost midsole.
Available sizes: 39, 40, 41, 42

Free shipping on orders over 50EUR.
DOC 3: FAQ - How to return an item?
Step 1: Log into your account
Step 2: Go to "My Orders"
Step 3: Click "Return"

Note: Items must be in original packaging.
Refund processed within 5-7 business days.
```

See the problem? Each document contains its own `\n` separators. The LLM has no way to know where `DOC 1` ends and `DOC 2` begins. The customer review, the empty lines, the multi-line FAQ steps they all look like document boundaries.

Now the LLM might cite "DOC 1" when referring to the return policy, which was actually the tail end of DOC 1 bleeding into DOC 2. Or it merges content from two documents and attributes it to one. You can't tell the difference between a correct citation and a broken one because the boundaries themselves are ambiguous.

## Why This Kills Your AgentOps Pipeline

If you're building a full agentic pipeline this becomes catastrophic, fast.

**Consider a typical agent flow:**

```
User question
    -> Retrieval (fetch docs)
        -> LLM reasoning (cite sources)
            -> Citation validator (check IDs exist)
                -> Response formatter (build answer with references)
                    -> Feedback loop (user clicks citation -> sees source)
```

When the LLM hallucinates `DOC 4`:

- Your **citation validator** passes because `4` looks like a valid integer, and you might have 4+ docs in your index. You'd need to check against the *exact set retrieved for this query*, not just "does this ID exist somewhere."
- Your **response formatter** links to the wrong document, or crashes on a missing reference.
- Your **feedback loop** shows the user a source that doesn't support the answer. Trust destroyed.
- Your **observability** logs show a "successful" pipeline run with green checkmarks everywhere. The hallucination is invisible in your metrics.

Now scale this. 100K conversations per day. How many silent citation errors are you shipping? You don't know, because sequential IDs make hallucination *look* valid.

With random IDs like `[XKJM]`, a hallucinated citation is immediately detectable: the string either exists in your retrieved set or it doesn't. No ambiguity, no edge cases. Your validator becomes a simple set lookup.

## Two Fixes

**1. Random letter IDs**

Use 3-4 random uppercase letters instead of numbers. No sequence to predict. Validation is a trivial set membership check.

**2. Mega separators**

Use 20 newlines between documents. This sounds absurd, but it works. The massive whitespace gap creates an unambiguous visual boundary that the LLM can't confuse with in-document formatting.

**Combined, the same example becomes clean:**

```
DOC [XKJM]: Nike Air Max 90
Available sizes: 40, 41, 42, 43
Colors: White, Black

Customer reviews:
"Great shoe, very comfortable" - Jean P.
"Runs small, order one size up" - Marie L.

Return policy: 30 days, unworn condition




















DOC [PLQW]: Adidas Ultraboost 22
Lightweight running shoe with Boost midsole.
Available sizes: 39, 40, 41, 42

Free shipping on orders over 50EUR.




















DOC [BNRT]: FAQ - How to return an item?
Step 1: Log into your account
Step 2: Go to "My Orders"
Step 3: Click "Return"

Note: Items must be in original packaging.
Refund processed within 5-7 business days.
```

Now the LLM can't confuse an in-document newline with a document boundary. And it can't hallucinate a citation because there's no pattern to extend.

## Implementation

```python
import random
import string
import re


def generate_doc_id(length: int = 4) -> str:
    """Generate a random uppercase letter ID."""
    return ''.join(random.choices(string.ascii_uppercase, k=length))


def format_docs_for_prompt(docs: list[str]) -> tuple[str, dict[str, str]]:
    """Format documents with random IDs and mega separators.

    Returns the formatted string and a mapping of ID -> document
    for downstream validation.
    """
    separator = "\n" * 20
    id_to_doc = {}
    formatted_parts = []

    for doc in docs:
        doc_id = generate_doc_id()
        id_to_doc[doc_id] = doc
        formatted_parts.append(f"DOC [{doc_id}]: {doc}")

    return separator.join(formatted_parts), id_to_doc


def validate_citations(response: str, valid_ids: set[str]) -> list[str]:
    """Extract cited IDs from response and flag any hallucinated ones."""
    cited = set(re.findall(r'\[([A-Z]{4})\]', response))
    hallucinated = cited - valid_ids
    return list(hallucinated)
```

The `id_to_doc` mapping makes your citation validator a one-liner. No fuzzy matching, no "does this number fall in range" logic. The cited ID exists or it doesn't.

---

## The Bigger Picture

This is why, as an AI Engineer, you need full control over your context. The prompt is where your bugs live. Citation hallucination, boundary confusion, token waste these are all context management problems.

AI frameworks abstract exactly this layer away from you. They build the prompt, format the documents, assemble the context and you can't see or control what's happening. As Hamel Husain puts it: [Fuck You, Show Me The Prompt](https://hamel.dev/blog/posts/prompt/).

If you want to go deeper on why this matters at scale, read my next post: [Why You Should Avoid AI Frameworks](why-avoid-ai-frameworks.md). They abstract the wrong things.

---

Have other RAG debugging tricks? [Reach out on LinkedIn](https://www.linkedin.com/in/wassim-trabelsi-3490aa11a).
