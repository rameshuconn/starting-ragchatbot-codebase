# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Retrieval-Augmented Generation (RAG) system for course materials built with FastAPI backend and vanilla JavaScript frontend. The system uses ChromaDB for vector storage, Anthropic's Claude API for AI generation, and provides semantic search over course documents.

## Architecture

The system follows a modular architecture with clear separation of concerns:

### Core Components
- **RAGSystem** (`backend/rag_system.py`): Main orchestrator that coordinates all components
- **VectorStore** (`backend/vector_store.py`): ChromaDB integration for vector storage and semantic search
- **AIGenerator** (`backend/ai_generator.py`): Anthropic Claude API integration with tool calling support
- **DocumentProcessor** (`backend/document_processor.py`): Handles document parsing and text chunking
- **SessionManager** (`backend/session_manager.py`): Manages conversation history per session
- **ToolManager/CourseSearchTool** (`backend/search_tools.py`): Search tools for AI function calling

### Data Flow
1. Documents in `docs/` are processed into chunks and stored in ChromaDB
2. User queries trigger semantic search via AI function calling
3. Retrieved chunks provide context for Claude to generate responses
4. Session management maintains conversation history

### Key Design Patterns
- Tool-based search: AI uses function calling to search rather than direct vector similarity
- Chunked document processing: Text split into overlapping chunks (800 chars, 100 overlap)
- Dual collections: Separate ChromaDB collections for course metadata vs content chunks
- Session-based conversations: Each user session maintains separate conversation history

## Common Commands

### Development Setup
```bash
# Install dependencies
uv sync

# Set up environment (requires ANTHROPIC_API_KEY in .env)
echo "ANTHROPIC_API_KEY=your_key_here" > .env
```

### Running the Application
```bash
# Quick start (recommended)
chmod +x run.sh
./run.sh

# Manual start
cd backend
uv run uvicorn app:app --reload --port 8000
```

### Working with Documents
- Course documents go in `docs/` folder (supports .txt, .pdf, .docx)
- Documents auto-load on server startup
- ChromaDB storage in `backend/chroma_db/`

## Configuration

Key settings in `backend/config.py`:
- `CHUNK_SIZE: 800` - Text chunk size for vector storage
- `CHUNK_OVERLAP: 100` - Character overlap between chunks  
- `MAX_RESULTS: 5` - Maximum search results returned
- `MAX_HISTORY: 2` - Conversation messages to remember
- `ANTHROPIC_MODEL: "claude-sonnet-4-20250514"` - Claude model used

## API Endpoints

- `POST /api/query` - Process queries with session support
- `GET /api/courses` - Get course analytics/statistics
- `/` - Serves frontend static files
- `/docs` - FastAPI auto-generated API documentation

## Development Notes

- Frontend uses vanilla JavaScript with no build process
- CORS enabled for development with wildcard origins
- Static files served with no-cache headers in development mode
- ChromaDB persistence automatically handled
- Session IDs auto-generated if not provided in requests

## Dependency Management

- Use `uv` for efficient Python dependency management
  - Use `uv sync` to install dependencies
  - Use `uv run` to run Python files or add dependencies