# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Course Materials RAG (Retrieval-Augmented Generation) system built with Python, FastAPI, and ChromaDB. The system enables users to query course materials and receive intelligent, context-aware responses using semantic search and AI-powered generation.

## Key Commands

### Development & Running
- **Start the application**: `./run.sh` or `cd backend && uv run uvicorn app:app --reload --port 8000`
- **Install dependencies**: `uv sync`
- **Access the app**: http://localhost:8000 (web interface), http://localhost:8000/docs (API docs)

### Environment Setup
- Create `.env` file in root with: `ANTHROPIC_API_KEY=your_api_key_here`
- Requires Python 3.13+, uv package manager, and Anthropic API key
- **Tip**: Use uv to run python files or add any dependencies

## Architecture & Code Structure

### Core System Architecture
The system follows a modular RAG architecture with these key components:

1. **RAGSystem** (`backend/rag_system.py`) - Main orchestrator that coordinates all components
2. **VectorStore** (`backend/vector_store.py`) - ChromaDB-based vector storage for semantic search
3. **AIGenerator** (`backend/ai_generator.py`) - Anthropic Claude integration for response generation
4. **DocumentProcessor** (`backend/document_processor.py`) - Processes course documents into chunks
5. **SessionManager** (`backend/session_manager.py`) - Manages conversation history
6. **ToolManager & SearchTools** (`backend/search_tools.py`) - Tool-based search system

### Data Models
- **Course** - Represents a complete course with lessons
- **Lesson** - Individual lesson within a course
- **CourseChunk** - Text chunks for vector storage
- All models defined in `backend/models.py` using Pydantic

### FastAPI Application Structure
- **Main app** (`backend/app.py`) - FastAPI application with CORS, static file serving
- **API endpoints**: `/api/query` (POST), `/api/courses` (GET)
- **Frontend**: Static files served from `frontend/` directory
- **Document loading**: Automatic loading from `docs/` folder on startup

### Key Configuration
Configuration managed through `backend/config.py`:
- Anthropic model: `claude-sonnet-4-20250514`
- Embedding model: `all-MiniLM-L6-v2`
- Chunk size: 800 characters with 100 character overlap
- ChromaDB storage: `./chroma_db`

### Document Processing Flow
1. Documents from `docs/` folder are processed on startup
2. Each document is parsed into a Course with Lessons
3. Content is chunked and stored in ChromaDB with metadata
4. Vector embeddings enable semantic search

### Query Processing Flow
1. User query received via `/api/query` endpoint
2. RAGSystem uses tool-based search to find relevant content
3. AI Generator creates response using Claude with retrieved context
4. Conversation history maintained per session

## Important Implementation Notes

- The system uses tool-based search rather than direct vector similarity
- ChromaDB collections store both course metadata and content chunks
- Session management enables conversation continuity
- Document processing includes duplicate detection to avoid re-processing
- Frontend is vanilla HTML/CSS/JS served as static files

## Vector Database Collections

The vector database has two collections:
- **course_catalog**:
  - stores course titles for name resolution
  - metadata for each course: title, instructor, course_link, lesson_count, lessons_json (list of lessons: lesson_number, lesson_title, lesson_link)
- **course_content**:
  - stores text chunks for semantic search
  - metadata for each chunk: course_title, lesson_number, chunk_index

## Important Development Notes

- Do not run `./run.sh`, I will always run it myself