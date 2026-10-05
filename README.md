# 📚 RAG Pipeline — PDF Question Answering with Groq

A **Retrieval-Augmented Generation (RAG) pipeline** that allows users to query information from multiple PDF documents using semantic search and a Large Language Model (LLM).

The system processes PDF documents, splits them into meaningful chunks, converts those chunks into vector embeddings, stores them in **ChromaDB**, retrieves the most relevant information for a query, and finally uses a **Groq-hosted LLM** to generate an answer based on the retrieved context.

---

## 🚀 Project Overview

This project demonstrates the complete RAG workflow:

```text
PDF Documents
      ↓
Document Loading
      ↓
Text Chunking
      ↓
Embedding Generation
      ↓
ChromaDB Vector Store
      ↓
Semantic Retrieval
      ↓
Relevant Context
      ↓
Groq LLM
      ↓
Generated Answer
```

The pipeline is designed to reduce hallucinations by providing the LLM with relevant information retrieved directly from the uploaded documents.

---

## ✨ Features

- 📄 Load multiple PDF documents
- ✂️ Split documents into smaller text chunks
- 🧠 Generate semantic embeddings using Sentence Transformers
- 🗄️ Store embeddings in persistent ChromaDB
- 🔎 Perform semantic similarity search
- 📊 Retrieve top-K relevant document chunks
- 🎯 Apply similarity score thresholds
- 🤖 Generate answers using Groq LLM
- 🔐 Use environment variables for API key management
- 💾 Persist the vector database for reuse

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| **Python** | Core programming language |
| **Google Colab** | Development environment |
| **LangChain** | Document processing and LLM integration |
| **PyPDFLoader** | Loading PDF documents |
| **RecursiveCharacterTextSplitter** | Text chunking |
| **Sentence Transformers** | Text embeddings |
| **all-MiniLM-L6-v2** | Embedding model |
| **ChromaDB** | Vector database |
| **Scikit-learn** | Similarity processing |
| **Groq** | LLM inference |
| **LangChain-Groq** | Groq integration |
| **Google Drive** | Persistent document/vector storage |

---

## 🧩 Pipeline Components

### 1. 📥 Document Ingestion

The pipeline loads all PDF files from a specified Google Drive directory using `PyPDFLoader`.

```python
loader = PyPDFLoader(pdf_path)
doc = loader.load()
```

All pages from the PDFs are combined into a document collection for further processing.

---

### 2. ✂️ Document Chunking

Large documents are divided into smaller chunks using LangChain's `RecursiveCharacterTextSplitter`.

```python
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50
)
```

### Configuration

- **Chunk Size:** 500
- **Chunk Overlap:** 50

Chunking makes semantic retrieval more efficient and allows the system to retrieve only the relevant portions of a document.

---

### 3. 🧠 Embedding Generation

The project uses the Sentence Transformers model:

```text
all-MiniLM-L6-v2
```

Each text chunk is converted into a numerical vector representation.

```python
self.model = SentenceTransformer("all-MiniLM-L6-v2")
```

These embeddings capture the semantic meaning of the text and allow similar queries and documents to be matched.

---

### 4. 🗄️ Vector Database

The generated embeddings are stored in **ChromaDB**.

```python
self.client = chromadb.PersistentClient(
    path=self.persist_directory
)
```

The project uses a persistent collection:

```text
pdf_documents
```

Each stored document contains:

- Unique document ID
- Text content
- Document metadata
- Document index
- Content length
- Embedding vector

---

### 5. 🔎 Semantic Retrieval

When a user submits a query, the query is converted into an embedding using the same Sentence Transformer model.

The embedding is then used to perform semantic search against ChromaDB.

```python
results = self.vector_store.collection.query(
    query_embeddings=[query_embeddings.tolist()],
    n_results=top_k
)
```

The system retrieves the most relevant chunks based on similarity.

The retriever also calculates a similarity score:

```python
similarity_score = 1 - distance
```

---

### 6. 🤖 LLM Integration

The retrieved documents are combined into a context and passed to a Groq-hosted LLM.

The project currently uses:

```text
openai/gpt-oss-20b
```

with:

```python
temperature=0.1
max_tokens=1024
```

The LLM receives a prompt containing the retrieved context and the user's query.

```text
Context:
[Retrieved document chunks]

Query:
[User question]
```

The model then generates the final answer based on the retrieved information.

---

## 🔄 Complete Workflow

### Step 1 — Load PDFs

```text
Google Drive → PDF Documents
```

### Step 2 — Extract Text

```text
PDF → Pages → Documents
```

### Step 3 — Create Chunks

```text
Documents → Text Chunks
```

### Step 4 — Generate Embeddings

```text
Text Chunks → Sentence Transformer → Vectors
```

### Step 5 — Store Vectors

```text
Vectors → ChromaDB
```

### Step 6 — Process Query

```text
User Query → Query Embedding
```

### Step 7 — Retrieve Context

```text
Query Embedding → ChromaDB → Top-K Relevant Chunks
```

### Step 8 — Generate Answer

```text
Retrieved Context + Query → Groq LLM → Final Answer
```

---

## 📂 Project Structure

```text
RAG-Pipeline/
│
├── RAG_Pipeline.ipynb
├── README.md
│
└── Data1/
    ├── document1.pdf
    ├── document2.pdf
    ├── ...
    │
    └── vector_store/
        └── ChromaDB data
```

> The notebook currently expects the PDF directory to be available through Google Drive.

---

## ⚙️ Installation

Install the required Python packages:

```bash
pip install langchain
pip install langchain-community
pip install langchain-text-splitters
pip install sentence-transformers
pip install chromadb
pip install scikit-learn
pip install groq
pip install langchain-groq
pypdf
```

Or install them together:

```bash
pip install langchain langchain-community langchain-text-splitters sentence-transformers chromadb scikit-learn groq langchain-groq pypdf
```

---

## 🔑 API Key Configuration

The project uses the Groq API.

Set your API key as an environment variable:

### Linux / macOS

```bash
export GROQ_API_KEY="your_api_key"
```

### Windows PowerShell

```powershell
$env:GROQ_API_KEY="your_api_key"
```

In Google Colab, you can configure the environment variable before running the LLM section.

The notebook accesses the key using:

```python
API_KEY_GROQ = os.environ["GROQ_API_KEY"]
```

**Never commit your API key to GitHub.**

---

## ▶️ Running the Project

### 1. Open the notebook

Open:

```text
RAG_Pipeline.ipynb
```

using Google Colab or Jupyter Notebook.

### 2. Mount Google Drive

The notebook uses:

```python
from google.colab import drive

drive.mount('/content/drive')
```

### 3. Add PDF Documents

Place your PDF files inside:

```text
MyDrive/Data1/
```

### 4. Run the ingestion pipeline

The notebook will:

```text
Load PDFs
   ↓
Create chunks
   ↓
Generate embeddings
   ↓
Store embeddings in ChromaDB
```

### 5. Run a query

Example:

```python
answer = generate_output(
    "What is ingestion and retrieval?",
    rag_retriever,
    llm
)

print(answer)
```

---

## 💡 Example Queries

You can ask questions related to the information contained in your PDFs, for example:

```text
What is RAG?

What is document ingestion?

How does retrieval work?

Explain the main concepts discussed in the document.

What are the key points from the uploaded PDFs?
```

The answer is generated using the relevant document chunks retrieved from ChromaDB.

---

## 🏗️ Architecture

```text
                 ┌─────────────────┐
                 │   PDF Documents │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │  PDF Loader     │
                 │  PyPDFLoader    │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │ Text Chunking   │
                 │ LangChain       │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │   Embeddings    │
                 │ MiniLM-L6-v2    │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │    ChromaDB     │
                 │  Vector Store   │
                 └────────┬────────┘
                          │
                    User Query
                          │
                          ▼
                 ┌─────────────────┐
                 │ Query Embedding │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │ Semantic Search │
                 │     Top-K       │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │ Retrieved       │
                 │ Context         │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │    Groq LLM     │
                 │ GPT-OSS-20B     │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │ Final Answer    │
                 └─────────────────┘
```

---

## 🎯 Why RAG?

Traditional LLM applications rely mainly on the knowledge learned during model training.

RAG adds an external knowledge retrieval layer:

```text
User Query
    ↓
Retrieve Relevant Information
    ↓
Provide Information to LLM
    ↓
Generate Grounded Response
```

This allows the application to answer questions based on a specific collection of documents rather than relying only on the model's internal knowledge.

---

## 📈 Key Learning Outcomes

Through this project, the following concepts are demonstrated:

- Retrieval-Augmented Generation
- Document ingestion
- Text preprocessing
- Chunking strategies
- Vector embeddings
- Semantic search
- Vector databases
- Similarity-based retrieval
- Context augmentation
- LLM integration
- Groq inference
- LangChain components

---

## 🔮 Future Improvements

Possible improvements to make the project production-ready:

- [ ] Build a Streamlit/React chat interface
- [ ] Add document upload functionality
- [ ] Support DOCX, TXT and web pages
- [ ] Add conversation memory
- [ ] Implement reranking
- [ ] Add citation/source references to answers
- [ ] Improve prompt engineering
- [ ] Add hybrid keyword + semantic search
- [ ] Add metadata-based filtering
- [ ] Implement evaluation metrics for retrieval quality
- [ ] Add authentication
- [ ] Deploy the complete application

---

## 👨‍💻 Author

**Anmol Kumar**

BCA — Information & Communication Technology

Interested in:

- 🤖 AI/ML
- 🧠 Generative AI
- 🔎 RAG Systems
- 💻 Software Engineering
- 📊 Data Science

### Connect With Me

- GitHub: `https://github.com/anmol392`
- LinkedIn: `https://www.linkedin.com/in/anmol-kumar-8656b8358/`
- LeetCode: `https://leetcode.com/u/Anmolkumar001/`

---

## ⭐ If You Like This Project

If this project helped you understand **RAG, embeddings, vector databases, or LLM integration**, consider giving the repository a ⭐.

---

## 📄 License

This project is intended for educational and research purposes.
