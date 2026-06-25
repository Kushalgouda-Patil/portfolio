---
title: "Vector Embeddings"
description: "Ever wondered how LLMs understand what you mean, not just what you type…"
publishDate: "2026-02-25"
tags: ["ai", "llm", "rag", "technology"]
---

*Ever wondered how LLMs understand what you mean, not just what you type…*

Vector Embeddings are numerical representations of data (text, audio, video etc). It is a mapping from input into list of floating point numbers. That list of numbers represents that input in embedding space of the embedding model

Embedding Model converts data into vector embeddings. Has its own dimension length, allowed input types and other characteristics. It helps to capture the semantic meaning of the data. Capturing the essence of the data allows us to perform similarity search between vectors to quantify the distance between 2 data points.

LLMs interpret the data through embeddings. The vector embeddings are foundational component for language models. They form backbone to the impressive capabilities of the LLMs since they provide language models the semantic understanding of token(data) and their relationships.

### **Similarity Metrics:**

**Cosine Similarity :** measure the similarity of two vectors by taking the cosine of the angle between the two vectors. (Based on only direction of the vectors)

cosine_similarity(a, b) = (a · b) / (‖a‖ ‖b‖)

**Dot product :** The dot product sums up the products of corresponding vector elements (Based on both direction and magnitude of vectors)

a · b = Σ(i=1 to n) aᵢbᵢ

### **Use cases:**

- **Search** (where results are ranked by relevance to a query string)
- **Clustering** (where text strings are grouped by similarity)
- **Recommendations** (where items with related text strings are recommended)
- **Anomaly detection** (where outliers with little relatedness are identified)
- **Diversity measurement** (where similarity distributions are analyzed)
- **Classification** (where text strings are classified by their most similar label)

I recently worked on a Retrieval Augmented Generation (RAG) project where vector embeddings played a central role.

The idea was simple: instead of letting the LLM answer questions purely from its training data, I embedded the documents and stored them in a vector database. Whenever a user asked a question, the query was converted into an embedding and compared against stored document vectors using similarity search. The most relevant chunks were then retrieved and passed to the LLM as context.

This approach helped the model generate responses grounded in real relevant data, reduced hallucinations, and made answers much more accurate and explainable.

I'll cover the full RAG pipeline, architecture, and implementation details in a separate blog.

Thanks