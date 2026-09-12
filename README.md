# Enterprise Policy Assistant 🛡️🤖

An enterprise-grade Retrieval-Augmented Generation (RAG) system designed to act as a secure, internal policy assistant for company employees. This AI agent answers questions strictly based on uploaded company policies (HR, IT, Travel, etc.) while actively defending against prompt injections, redacting PII, and evaluating its own answers for hallucinations (groundedness).

Built as a portfolio project demonstrating modern AI Engineering practices, secure LLM integration, and robust RAG architectures.

## ✨ Key Features

*   **Retrieval-Augmented Generation (RAG):** Uses a local vector database (ChromaDB) to retrieve relevant document chunks to ground the LLM's answers in actual company policy.
*   **Multi-Format Document Support:** Automatically ingests, parses, and chunks `.pdf`, `.docx`, `.md`, and `.txt` files.
*   **Prompt Injection Defense:** Employs both deterministic regex patterns and a secondary "Security LLM" judge to detect and block jailbreak attempts or system prompt extraction.
*   **PII Redaction:** Automatically scrubs Personally Identifiable Information (like Emails, Phone Numbers, Aadhaar, PAN, and Credit Cards) from user inputs before they are sent to the LLM.
*   **Anti-Hallucination (Groundedness) Checks:** Uses a strict evaluator LLM to score the final generated answer against the retrieved context, ensuring the assistant doesn't invent policies.
*   **Interactive UI:** A clean, chat-based interface built entirely in Python using Streamlit.

## 🛠️ Technology Stack

*   **Frameworks:** [LangChain](https://python.langchain.com/) (Core orchestration, Document Loaders, Prompts)
*   **UI:** [Streamlit](https://streamlit.io/)
*   **Vector Database:** [ChromaDB](https://www.trychroma.com/)
*   **Models:** OpenAI (`gpt-4o-mini` for generation/evaluation, `text-embedding-3-small` for embeddings)
*   **Data Validation:** Pydantic

## 📂 Project Structure

```
enterprise-policy-assistant/
├── app.py                      # Streamlit UI and application entry point
├── src/                        # Core Application Logic
│   ├── rag_pipeline.py         # Orchestrates the RAG flow (Retrieve -> Generate)
│   ├── document_loader.py      # Parses and chunks PDFs, DOCX, Markdown, Text
│   ├── vector_store.py         # Manages ChromaDB embeddings and retrieval
│   ├── guardrails.py           # PII redaction, Prompt Injection & Groundedness checks
│   ├── schemas.py              # Pydantic data models for structured inputs/outputs
│   ├── evaluation.py           # Red-team evaluation scripts
│   └── config.py               # Environment configuration and settings
├── data/                       # Local data storage
│   ├── policies/               # Put your company policy documents here
│   └── evaluation/             # Golden datasets and red-team testing datasets
├── .env.example                # Example environment variables
└── requirements.txt            # Python dependencies
```

## 🚀 Getting Started

### Prerequisites
*   Python 3.10+
*   An OpenAI API Key

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Yagnik-patel24/enterprise-policy-assistant.git
   cd enterprise-policy-assistant
   ```

2. **Create a virtual environment (Recommended):**
   ```bash
   python -m venv venv
   # On Windows:
   venv\Scripts\activate
   # On macOS/Linux:
   source venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up Environment Variables:**
   * Copy `.env.example` to a new file named `.env`.
   * Open `.env` and add your `OPENAI_API_KEY`.

5. **Run the Application:**
   ```bash
   streamlit run app.py
   ```
   The app will automatically start a local server, usually accessible at `http://localhost:8501`.

## 🛡️ Security Architecture (How it works under the hood)

When a user submits a question:
1. **Input Sanitization:** The input is scanned by `guardrails.py`. PII is redacted (e.g., `yagnik@company.com` becomes `[REDACTED_EMAIL]`).
2. **Injection Detection:** The input is checked against known jailbreak patterns. If suspicious, it is routed to a Security LLM to classify intent.
3. **Retrieval:** If safe, the question is converted to an embedding and queried against ChromaDB to find the top matching policy sections.
4. **Generation:** The context and question are sent to the primary LLM with strict instructions to *only* use the provided context.
5. **Groundedness Check:** The generated answer is sent to an Evaluator LLM to ensure it is factually supported by the retrieved context. If it fails, a fallback message is shown.

---
*Built as a demonstration of production-ready AI Engineering.*
