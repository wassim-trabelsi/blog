---
date: 2025-11-10
authors:
  - wassim-trablesi
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
description: Two simple tricks to prevent LLMs from hallucinating document citations in your RAG pipeline
---

# Stop Citation Hallucination with Better Document Identifiers

When building a RAG, it’s common practice to feed multiple document chunks to the generator and ask it to cite which documents were used to generate an answer. The standard approach is to assign incremental numbers to each document and ask the LLM to cite the indices it used to formulate its answer. **This breaks more often than you’d think**


## Problem setup

Consider a RAG system that processes a set of documents as input, where the documents originate from sources you don’t directly manage or control.

```python
documents = [
  {
    "content": "In software engineering, unit tests help ensure code reliability [1].\nDevelopers often integrate them into CI/CD pipelines [2]."
  },
  {
    "content": "Python is widely used for machine learning and data analysis [3].\nLibraries such as NumPy and PyTorch simplify tensor operations [4]."
  },
  {
    "content": "Version control systems (e.g., Git) enable collaboration among developers [5].\nBranching strategies like GitFlow help manage large projects [6]."
  },
  {
    "content": "APIs allow different software components to communicate [7].\nREST and GraphQL are two common architectural styles [8]."
  }
]
```

These documents are stored and processed by the system for retrieval and grounding during question answering.


Then they ask the LLM to cite which document number it used. The problem? **Numbers are way broadly used in documents and have an incremental property that invites hallucination.**

When your documents contain messy separators or unclear boundaries, the LLM might hallucinate the next number in sequence. It sees DOC 1, DOC 2, DOC 3... and confidently invents DOC 4 or DOC 5 that never existed. **Your entire citation validation pipeline collapses**

I've debugged production systems where this caused cascading failures. In one case, a log analysis tool identified which log contained an issue, but the logs themselves contained the same separators used between documents. The LLM couldn't distinguish document boundaries from content, citations fell apart, and nobody trusted the system.

## Two Magic Tricks

### Trick 1: Use Random Letter Sequences (Cleaner Solution)

Replace numerical indexes with random 3-4 letter sequences:

```
DOC [XKJM]: Paris is the capital of France
DOC [PLQW]: Wassim likes doing math
DOC [BNRT]: Machine learning requires data
```

Random letter sequences don't follow predictable patterns. And you are very unlikely to find them occur inside the documents themselves. The LLM can't hallucinate "the next one" because there's no obvious sequence. Three or four letters provide enough uniqueness to identify documents without ambiguity.

### Trick 2: Use Mega Separators (Quick Debug)

When debugging citation issues, use overkill separators like 20 newlines between documents:

```python
separator = "\n" * 20
documents = separator.join([f"DOC {id}: {content}" for id, content in doc_list])
```

Yes, this costs ~20 extra tokens per document. But it's worth it to avoid mixing:

```
DOC 1 Paris is the capital of France

Paris is the capital of France
```

with:

```
DOC 2 Wassim likes doing math
```

Don't worry about token consumption during debugging. You're paying a tiny amount to prevent your system from failing completely.

## Real Example: Log Analysis Failure

I debugged a production application that identified which log entries contained issues. The system used single newlines (`\n`) to separate log documents. But logs themselves contain multiple newlines internally. The LLM couldn't determine where one log ended and the next began, so it hallucinated log references.

Fix? Switch to mega separators during debugging, then use random letter IDs in production. System started working immediately.

## Implementation Pattern

Here's a practical pattern:

```python
import random
import string

def generate_doc_id(length=4):
    return ''.join(random.choices(string.ascii_uppercase, k=length))

def prepare_documents_for_llm(documents):
    doc_ids = [generate_doc_id() for _ in documents]
    separator = "\n" * 20

    formatted = []
    for doc_id, content in zip(doc_ids, documents):
        formatted.append(f"DOC {doc_id}:\n{content}")

    return separator.join(formatted), doc_ids
```

Your citation validation becomes:

```python
def validate_citation(cited_id, valid_ids):
    return cited_id in valid_ids  # No more off-by-one hallucinations
```

## Takeaway

Citation hallucination in RAG systems often stems from predictable document identifiers and weak separators. Two quick fixes:

1. Use random letter sequences instead of numbers
2. Use overkill separators when debugging

These simple changes prevent LLMs from hallucinating citations that break your validation pipeline.

---

Have other RAG debugging tricks? Leave a comment below or [reach out on LinkedIn](https://www.linkedin.com/in/wassim-trabelsi-3490aa11a).
