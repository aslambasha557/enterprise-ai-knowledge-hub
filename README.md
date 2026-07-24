# Enterprise AI Knowledge Hub 🧠🔐

> **Air-Gapped, On-Premises RAG Infrastructure for Enterprise Data Intelligence**

An enterprise-ready Retrieval-Augmented Generation (RAG) platform designed to index, structure, and query corporate knowledge repositories securely behind company firewalls.

---

## 🏛 Solution Architecture

```mermaid
graph TD
    A[Enterprise Knowledge Sources<br/>Google Drive / SharePoint / Shares] -->|Document Ingestion| B[FastAPI Document Extractor]
    B -->|Clean Text & Metadata| C[Recursive Character Splitter]
    C -->|Chunk Vectors| D[Ollama Embedder<br/>nomic-embed-text]
    D -->|Index Storage| E[Qdrant Vector Database]
    
    F[User Query Interface] -->|Natural Language Query| G[n8n Orchestration Engine]
    G -->|Vector Context Search| E
    E -->|Relevant Context Chunks| G
    G -->|Context + Query Prompt| H[Local LLM / Gemini API]
    H -->|Audited Response| F
