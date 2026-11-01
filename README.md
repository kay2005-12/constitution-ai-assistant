# ⚖️ Constitution-AI-Assistant

**An AI-powered legal assistant for the Constitution of India using Agentic RAG, Hybrid Retrieval, and a fine-tuned Qwen model.**

Constitution-AI-Assistant is a research-oriented project that explores how retrieval-augmented generation can be used to answer questions about the Constitution of India. The project combines semantic search, keyword-based retrieval, rank fusion, a LangGraph workflow, and Qwen with LoRA fine-tuning.

The main objective is to retrieve relevant constitutional provisions and use them as context for generating answers.

## 📌 Project Status

The project has progressed through knowledge-base preparation, hybrid retrieval implementation, preliminary retrieval testing, Qwen LoRA fine-tuning, and LangGraph workflow development.

Some components remain experimental, particularly answer quality, relevance evaluation, and output handling.

## ✅ Work Completed

### 1. Constitutional Knowledge Base

Prepared a structured JSON knowledge base containing approximately **465 records** representing constitutional content.

The records include fields such as:

- `article` — constitutional article identifier
- `title` — article title or heading
- `description` — associated constitutional text or description

The dataset includes the Preamble and alphanumeric article identifiers. The structured records are used as the source material for retrieval.

### 2. Semantic Retrieval with FAISS

Implemented semantic retrieval using:

- **Embedding model:** `BAAI/bge-small-en-v1.5`
- **Vector index:** FAISS

The embedding model represents text and queries as vectors, allowing retrieval based on semantic similarity rather than requiring exact keyword matches.

### 3. Keyword Retrieval with BM25

Implemented a BM25-based keyword retrieval component using a BM25 retriever.

BM25 helps identify documents containing relevant terms from the user's query. It complements semantic retrieval, particularly when questions mention specific legal terminology or article-related keywords.

### 4. Hybrid Retrieval with Reciprocal Rank Fusion

Combined FAISS semantic retrieval and BM25 keyword retrieval using **Reciprocal Rank Fusion (RRF)**.

The retrieval process is:

1. Receive a legal question.
2. Retrieve semantically similar records using FAISS.
3. Retrieve keyword-relevant records using BM25.
4. Fuse the ranked results using RRF.
5. Return the highest-ranked documents as context for the next stage.

This implementation combines two retrieval approaches instead of relying exclusively on either semantic similarity or keyword matching.

### 5. Preliminary Retrieval Evaluation

Tested the hybrid retrieval pipeline using a small set of constitutional questions.

In the initial three-question test, the expected constitutional article appeared within the **top five retrieved results for all three questions**.

| Evaluation item | Observed result |
|---|---|
| Preliminary test questions | 3 |
| Questions with expected article in top 5 | 3 |
| Preliminary Hit Rate@5 | 100% |

**Important:** This is a small preliminary test, not a comprehensive benchmark. It does not establish general retrieval accuracy or prove that generated answers are legally correct.

### 6. Qwen LoRA Fine-Tuning

Fine-tuned a Qwen language model using parameter-efficient fine-tuning with **LoRA and PEFT**.

The training configuration includes:

| Parameter | Value |
|---|---|
| LoRA rank (`r`) | 16 |
| LoRA alpha | 32 |
| LoRA dropout | 0.05 |
| Target modules | `q_proj`, `k_proj`, `v_proj`, `o_proj`, `up_proj`, `down_proj`, `gate_proj` |
| Training framework | Transformers, PEFT, TRL |
| Training configuration | 2 epochs |
| Per-device batch size | 1 |
| Gradient accumulation steps | 8 |
| Learning rate | 0.0002 |

The LoRA adapter was saved and downloaded from Colab for local testing. The local environment was able to load the PEFT model and its active adapter.

LoRA updates a smaller set of trainable parameters rather than requiring full-model fine-tuning. The configuration above documents the training setup; it does not, by itself, establish the quality of the fine-tuned model.

### 7. Agentic RAG Workflow with LangGraph

Implemented a LangGraph workflow to coordinate retrieval, context evaluation, query rewriting, and response generation.

The workflow contains the following nodes:

- **Retrieve:** Calls the hybrid retrieval function to obtain relevant documents.
- **Evaluate:** Uses a lexical-overlap heuristic to assess the relationship between the question and retrieved context.
- **Rewrite Query:** Reformulates the query when the retrieved context is considered insufficient.
- **Generate:** Uses the loaded language model to generate a response.
- **Conditional Routing:** Determines whether the workflow should proceed to generation or attempt retrieval again.
- **Retry Tracking:** Maintains a retry counter in the workflow state.

The state tracks fields including the original question, search query, retrieved documents, relevance status, retry count, and generated answer.

**Current limitation:** The relevance evaluator is a heuristic, not a trained legal relevance classifier or a comprehensive LLM-based judge. A lexical match does not guarantee that the retrieved passage fully answers the question.

### 8. Model and Retrieval Testing

The components have been tested incrementally:

- Hybrid retrieval was checked against constitutional questions.
- Relevant provisions, including Article 15 for a discrimination-related query, were observed among retrieved results.
- The Qwen model and its LoRA adapter were loaded for local inference testing.
- The LangGraph workflow was developed to connect retrieval, relevance checks, query rewriting, and generation.

Generation testing also revealed output-handling issues: the model can emit `<think>` blocks, and some responses may be incomplete or truncated. These remain areas for improvement.

## 🧰 Technologies Used

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| JSON | Constitutional knowledge-base storage |
| BAAI BGE Small EN | Text embeddings |
| FAISS | Semantic vector retrieval |
| BM25 | Keyword retrieval |
| Reciprocal Rank Fusion | Combining retrieval rankings |
| Qwen | Language model |
| PEFT / LoRA | Parameter-efficient fine-tuning |
| Transformers | Model loading and inference |
| TRL | Supervised fine-tuning tooling |
| LangGraph | Stateful retrieval and generation workflow |

## 🧪 Current Limitations

- Retrieval evaluation has only been performed on a small preliminary test set.
- The lexical-overlap evaluator may incorrectly classify relevant or irrelevant context.
- Generated answers can contain reasoning tags or be truncated.
- Comprehensive faithfulness, answer relevance, and legal correctness evaluations have not yet been established.
- The current implementation should not be treated as a substitute for authoritative legal sources or professional legal advice.

## 🚧 Remaining Work

The following items are not documented as completed features:

- Larger, reproducible retrieval evaluation with Recall@K, Precision@K, and MRR.
- Systematic answer evaluation for faithfulness and relevance.
- Reliable handling of incomplete or malformed model outputs.
- PDF ingestion and text extraction.
- Audio/video transcription and ingestion.
- MCP integration where external tools or live data are needed.
- Docker packaging and deployment.
- Production-ready web application deployment.

These items should be marked complete only after implementation and testing.

## 🔒 Legal Disclaimer

This project is intended for educational and research purposes. AI-generated responses may be inaccurate, incomplete, or outdated. Always verify important constitutional questions against authoritative legal sources and consult a qualified legal professional when necessary.

## 👨‍💻 Author

**Syed Kaysan Ul Islam**

GitHub: [@kay2005-12](https://github.com/kay2005-12)

---

⭐ If you find this project interesting, consider starring the repository.
