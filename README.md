# PDF ChatBot with LangChain

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![LangChain](https://img.shields.io/badge/framework-LangChain-purple)](https://python.langchain.com/)
[![RAG](https://img.shields.io/badge/approach-RAG-orange)](https://arxiv.org/abs/2005.11401)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An intelligent document question-answering system that allows you to upload PDF documents and ask natural language questions about their content. Powered by LangChain's RAG (Retrieval-Augmented Generation) pipeline.

## 📋 Overview

This project demonstrates how to build a **Retrieval-Augmented Generation (RAG)** application for PDF document analysis. Users can upload PDF files and ask questions about the content, with the system retrieving relevant sections and generating accurate answers using LLMs.

### Key Highlights
- 📄 **PDF Processing** - Extract and chunk PDF documents efficiently
- 🔍 **Semantic Search** - Find relevant content using embeddings
- 🧠 **Context-Aware Answers** - Generate answers based on document context
- 💾 **Vector Store** - Persistent embedding storage for fast retrieval
- 🎯 **Accurate Responses** - RAG approach ensures factual, document-grounded answers
- 🔐 **Privacy-Focused** - Can run with local LLMs for complete privacy

## 🎯 Features

- **PDF Upload & Processing** - Handle multiple PDF documents
- **Intelligent Document Chunking** - Smart splitting with overlap for context
- **Semantic Similarity Search** - Find relevant sections using embeddings
- **Chat History** - Maintain conversation context
- **Multi-Document Support** - Query across multiple PDFs
- **Streaming Responses** - Real-time response generation
- **Source Attribution** - Know which document the answer came from
- **Flexible LLM Support** - Use OpenAI, Hugging Face, or local models
- **Vector Store Persistence** - Cache embeddings for faster queries

## 🛠 Tech Stack

| Component | Technology |
|-----------|-----------|
| **Framework** | LangChain |
| **Vector Store** | Chroma / FAISS / Pinecone |
| **Embeddings** | OpenAI / HuggingFace / Sentence Transformers |
| **LLM** | OpenAI GPT / Local LLMs |
| **PDF Processing** | PyPDF / pdfplumber |
| **UI** | Streamlit / Chainlit |
| **Language** | Python 3.8+ |

## 📦 Installation

### Prerequisites
- Python 3.8 or higher
- pip or conda
- PDF files to process
- API key for LLM (or use local models)

### Quick Setup

```bash
# Clone the repository
git clone https://github.com/abhilashpanda04/PDF-ChatBot-Langchain.git
cd PDF-ChatBot-Langchain

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set up environment variables
cp .env.example .env
# Edit .env with your API keys
```

### Environment Variables

Create a `.env` file:

```bash
# LLM Configuration
OPENAI_API_KEY=your_openai_key

# Vector Store (optional)
PINECONE_API_KEY=your_pinecone_key
PINECONE_ENVIRONMENT=us-west1-gcp

# Hugging Face (optional)
HUGGINGFACE_API_KEY=your_hf_key

# Optional settings
CHUNK_SIZE=1000
CHUNK_OVERLAP=200
```

## 🚀 Usage

### Run the Application

```bash
# Using Streamlit UI
streamlit run app.py

# Using Chainlit UI
chainlit run app.py

# Command-line interface
python cli.py --pdf documents/sample.pdf --query "What is the main topic?"
```

### Basic Usage Example

```python
from pdf_chatbot import PDFChatBot

# Initialize chatbot
chatbot = PDFChatBot(
    pdf_path="documents/sample.pdf",
    embedding_model="openai",
    llm_model="gpt-3.5-turbo"
)

# Ask a question
response = chatbot.query("What are the key findings?")
print(response)

# Get source information
print(chatbot.get_sources())
```

## 📁 Project Structure

```
pdf-chatbot-langchain/
├── app.py                      # Streamlit/Chainlit main app
├── cli.py                      # Command-line interface
├── pdf_chatbot/
│   ├── __init__.py
│   ├── chatbot.py             # Main chatbot class
│   ├── document_loader.py     # PDF loading and processing
│   ├── embeddings.py          # Embedding configuration
│   ├── vector_store.py        # Vector store initialization
│   ├── llm.py                 # LLM configuration
│   └── chains.py              # LangChain pipelines
├── documents/                 # Sample PDFs
│   └── sample.pdf
├── vector_stores/             # Stored embeddings
├── prompts/
│   ├── system_prompts.py
│   └── retrieval_prompts.py
├── utils/
│   ├── helpers.py
│   ├── validators.py
│   └── config.py
├── requirements.txt
├── .env.example
└── README.md
```

## ⚙️ Configuration

### Chunk Size and Overlap

```python
# Optimal settings for different use cases
CHUNK_SIZE = 1000      # Characters per chunk
CHUNK_OVERLAP = 200    # Overlap between chunks

# For dense documents: larger chunks
# For Q&A: smaller chunks with high overlap
```

## 🔧 Examples

### Basic RAG Pipeline

```python
from langchain.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.embeddings import OpenAIEmbeddings
from langchain.vectorstores import Chroma
from langchain.chains import RetrievalQA
from langchain.chat_models import ChatOpenAI

# Load PDF
loader = PyPDFLoader("document.pdf")
documents = loader.load()

# Split documents
splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200
)
texts = splitter.split_documents(documents)

# Create embeddings and vector store
embeddings = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(texts, embeddings)

# Create QA chain
llm = ChatOpenAI(temperature=0)
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    chain_type="stuff",
    retriever=vectorstore.as_retriever()
)

# Query
response = qa_chain.run("What is this document about?")
```

### With Source Attribution

```python
from langchain.chains import RetrievalQAWithSourcesChain

qa_with_sources = RetrievalQAWithSourcesChain.from_chain_type(
    llm=llm,
    chain_type="stuff",
    retriever=vectorstore.as_retriever()
)

result = qa_with_sources({"question": "Key points?"})
print("Answer:", result["answer"])
print("Sources:", result["sources"])
```

### Streamlit Interface

```python
import streamlit as st
from pdf_chatbot import PDFChatBot

st.set_page_config(page_title="PDF ChatBot", layout="wide")
st.header("📄 PDF Question Answering")

# File upload
uploaded_file = st.file_uploader("Upload a PDF", type=["pdf"])

if uploaded_file:
    # Process PDF
    chatbot = PDFChatBot(uploaded_file.getvalue())
    
    # Chat interface
    user_query = st.text_input("Ask a question about the PDF:")
    
    if user_query:
        response = chatbot.query(user_query)
        st.write("**Answer:**", response["answer"])
        
        with st.expander("📚 Sources"):
            for doc in response["source_documents"]:
                st.write(f"Page {doc.metadata.get('page', 'N/A')}")
                st.write(doc.page_content[:200] + "...")
```

## 🎓 Advanced Features

### Custom Retrieval

```python
# Change retrieval strategy
retriever = vectorstore.as_retriever(
    search_type="mmr",  # Maximum Marginal Relevance
    search_kwargs={"k": 5, "fetch_k": 20}
)
```

### Prompt Customization

```python
from langchain.prompts import PromptTemplate

custom_prompt = PromptTemplate(
    template="""You are an expert document analyst.
Context: {context}
Question: {question}
Answer with specific details from the document.""",
    input_variables=["context", "question"]
)

qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=vectorstore.as_retriever(),
    chain_type_kwargs={"prompt": custom_prompt}
)
```

### Local LLM Setup (Ollama)

```python
from langchain.llms import Ollama

llm = Ollama(
    model="llama2",
    base_url="http://localhost:11434"
)
```

## 🚢 Deployment

### Hugging Face Spaces

```bash
huggingface-cli login
# Push your repository
```

### Docker

```dockerfile
FROM python:3.9
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["streamlit", "run", "app.py"]
```

```bash
docker build -t pdf-chatbot .
docker run -p 8501:8501 pdf-chatbot
```

### AWS Lambda with FastAPI

```python
from fastapi import FastAPI
from mangum import Mangum

app = FastAPI()

@app.post("/query")
async def query_pdf(pdf_path: str, question: str):
    chatbot = PDFChatBot(pdf_path)
    return chatbot.query(question)

handler = Mangum(app)
```

## 📊 Performance Optimization

### Vector Store Selection

| Store | Speed | Cost | Persistence |
|-------|-------|------|-------------|
| **Chroma** | ⭐⭐⭐ | Free | Local |
| **FAISS** | ⭐⭐⭐⭐ | Free | Manual |
| **Pinecone** | ⭐⭐⭐⭐⭐ | Paid | Cloud |
| **Weaviate** | ⭐⭐⭐⭐ | Free | Cloud |

### Chunking Strategy

```python
# For better retrieval quality
chunk_sizes = {
    "research_papers": 1500,
    "legal_documents": 1000,
    "technical_docs": 800,
    "general_text": 1000
}
```

## 🐛 Troubleshooting

### Out of Memory Issues
- Reduce `chunk_size`
- Use smaller embedding model
- Process documents in batches

### Poor Answer Quality
- Increase `chunk_size` for context
- Try different embedding model
- Adjust retrieval parameters (k, fetch_k)

## 📚 Resources

- [LangChain Documentation](https://python.langchain.com/)
- [RAG Paper](https://arxiv.org/abs/2005.11401)
- [Vector Databases Guide](https://www.pinecone.io/learn/vector-database/)
- [Prompt Engineering Tips](https://platform.openai.com/docs/guides/prompt-engineering)

## 📝 License

This project is licensed under the MIT License - see LICENSE file for details.

## 👤 Author

**Abhilash Kumar Panda**
- 📧 Email: abhilashk.isme1517@gmail.com
- 🔗 LinkedIn: [Abhilash Kumar Panda](https://www.linkedin.com/in/abhilash-kumar-panda/)
- 🌐 Portfolio: [abhilashpanda04.github.io](https://abhilashpanda04.github.io/Portfolio_site/)
- GitHub: [@abhilashpanda04](https://github.com/abhilashpanda04)

---

⭐ If this helps you build amazing applications, please star the repo!
