# React Agent

A notebook-based ReAct assistant that can answer questions using an ABC Company document, search the web, and find research papers on arXiv. The project also includes a separate notebook for preparing a retrieval-augmented generation (RAG) index from the company PDF.

## Project Structure

```text
agent/agent.ipynb   Interactive agent with RAG, web, and arXiv tools
agent/chroma_db/    Chroma database used by the agent notebook
data/               Source PDF for the company-document RAG workflow
rag/rag.ipynb       PDF loading, chunking, embedding, and vector-store setup
rag/chroma_db/      Chroma database created by the RAG notebook
src/react_agent/    Python package scaffold
```

## Requirements

- Python and Jupyter Notebook support in VS Code, or another Jupyter environment
- A Groq API key for chat completions
- A Google Gemini API key for document embeddings
- Internet access for the Groq, Gemini, web-search, and arXiv services

Create and activate a virtual environment from the project root in PowerShell:

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install jupyter ipykernel langchain langchain-chroma langchain-google-genai langchain-groq langchain-community langchain-experimental python-dotenv pypdf ddgs arxiv pydantic
```

Create a `.env` file in the project root (you can start from `.envexample`) and add your own keys:

```dotenv
GROQ_API_KEY=your_groq_api_key
GEMINI_API_KEY=your_gemini_api_key
```

Do not commit `.env`; it is excluded by `.gitignore`.

## Prepare the RAG Index

Open `rag/rag.ipynb`, select the virtual environment as its kernel, and run the cells from top to bottom. The notebook loads `data/ABC_Company_RAG_Learning_Document.pdf`, creates semantic chunks and Gemini embeddings, then writes a Chroma collection under `rag/chroma_db/`.

The RAG and agent notebooks currently use different Chroma directories: the RAG notebook writes to `rag/chroma_db/`, while the agent opens `agent/chroma_db/`. To have the agent query the index you just built, configure both notebooks to use the same `persist_directory` and collection name (`abc_company`). Keep the notebook working directory in mind when setting a relative path.

## Run the Agent

1. Open `agent/agent.ipynb` and select the same Python environment.
2. Run the setup, tool definition, and agent creation cells in order.
3. Run the final interactive cell and enter a question when prompted.
4. Research-paper questions are searched on arXiv. The returned paper title and link are validated with Pydantic before display. Other questions are handled by the agent using its available tools.
5. Answer the continue prompt with `no` to end the conversation.

The conversation's question-and-answer strings are kept in the in-memory list `conversation_responses` for later summarizing. This list is not saved to disk and is cleared when the notebook kernel is restarted or the cell that initializes it is rerun.

## Notes

- Web search and arXiv results depend on external services and can change between runs.
- The `src/react_agent` directory is currently a minimal package scaffold; the runnable workflows are in the notebooks.
