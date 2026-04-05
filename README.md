# 🤖 customer-support-rag-bot

> AI-powered customer support chatbot using Retrieval-Augmented Generation (RAG) — automatically ingests documents from Google Drive, stores embeddings in Pinecone, and answers user queries via an intelligent AI Agent.

---

## 📌 Overview

This project implements a two-pipeline RAG architecture built with **n8n**, enabling a smart customer support Q&A bot that grounds its answers in your own documentation. When a new file lands in Google Drive, it's automatically chunked, embedded, and stored. When a user asks a question in chat, the AI Agent retrieves the most relevant context from the vector store and responds accurately.

---

## 🏗️ Architecture

### Pipeline 1 — Document Ingestion

```
Google Drive Trigger → Download File → Pinecone Vector Store
                                            ├── OpenAI Embeddings
                                            └── Default Data Loader
```

| Step | Node | Description |
|------|------|-------------|
| 1 | **Google Drive Trigger** | Fires on `fileCreated` event in a watched folder |
| 2 | **Download File** | Downloads the newly created file as binary data |
| 3 | **Default Data Loader** | Parses the file content into document chunks |
| 4 | **OpenAI Embeddings** | Converts chunks into vector embeddings |
| 5 | **Pinecone Vector Store** | Stores the embeddings for semantic retrieval |

---

### Pipeline 2 — Chat Query & Response

```
When Chat Message Received → AI Agent
                                  ├── OpenAI Chat Model
                                  ├── Simple Memory
                                  └── Pinecone Vector Store (Tool)
                                            └── OpenAI Embeddings
```

| Step | Node | Description |
|------|------|-------------|
| 1 | **Chat Trigger** | Receives user message from the chat interface |
| 2 | **OpenAI Chat Model** | Powers the agent's language understanding (GPT-4 recommended) |
| 3 | **Simple Memory** | Maintains short-term conversation context per session |
| 4 | **Pinecone Vector Store** | Used as a retrieval tool to fetch relevant documents |
| 5 | **OpenAI Embeddings** | Embeds the user query for similarity search |

---

## 🛠️ Tech Stack

| Tool | Role |
|------|------|
| [n8n](https://n8n.io) | Workflow automation platform |
| [OpenAI](https://openai.com) | Chat model + text embeddings |
| [Pinecone](https://pinecone.io) | Vector database for semantic search |
| [Google Drive](https://drive.google.com) | Document source & ingestion trigger |

---

## 🚀 Getting Started

### Prerequisites

- n8n instance (self-hosted or cloud)
- OpenAI API key
- Pinecone account + index created
- Google Drive API credentials (OAuth2)

### Setup

1. **Clone or import the workflows** into your n8n instance.

2. **Configure credentials** in n8n:
   - `Google Drive OAuth2`
   - `OpenAI API`
   - `Pinecone API`

3. **Create a Pinecone index** with:
   - Dimensions: `1536` (for `text-embedding-ada-002`) or `3072` (for `text-embedding-3-large`)
   - Metric: `cosine`

4. **Activate Pipeline 1** — point the Google Drive Trigger to your support documents folder.

5. **Upload your documents** (PDFs, DOCX, TXT) to the watched Google Drive folder. They will be automatically ingested.

6. **Activate Pipeline 2** — open the chat interface via the **Open Chat** button and start asking questions.

---

## 💬 Usage

Once both pipelines are active:

1. Drop any support document (FAQ, manual, policy doc) into your Google Drive folder.
2. The ingestion pipeline runs automatically and indexes the content.
3. Open the chat interface and ask questions in natural language.
4. The AI Agent retrieves relevant document chunks and returns a grounded, accurate answer.

---

## 📁 Project Structure

```
customer-support-rag-bot/
├── workflows/
│   ├── pipeline_1_ingestion.json     # Google Drive → Pinecone ingestion flow
│   └── pipeline_2_chat_agent.json    # Chat trigger → AI Agent query flow
├── docs/
│   └── architecture_diagram.png      # Visual workflow overview
└── README.md
```

---

## 🔧 Configuration Tips

- **Chunk size**: Tune the Default Data Loader's chunk size based on your document type. Smaller chunks (200–500 tokens) work better for precise Q&A.
- **Top-K retrieval**: Adjust how many chunks the Pinecone tool retrieves per query (3–5 is a good starting point).
- **System prompt**: Customize the AI Agent's system prompt to reflect your brand's tone and support scope.
- **Memory window**: Simple Memory retains recent turns — useful for follow-up questions within a session.

---

## 📄 License

MIT License. Feel free to use, modify, and distribute.

---

## 🙌 Contributing

Pull requests are welcome! For major changes, please open an issue first to discuss what you'd like to change.
