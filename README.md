# Automated RAG Synchronization System

A scheduled automation pipeline built with n8n that keeps a Pinecone vector database in sync with a Google Drive knowledge base — automatically and without duplication.

---

## The Problem

AI agents that use RAG need an up-to-date knowledge base to answer accurately. The common approach — re-uploading all files every time — wastes API calls and increases cost. There was no clean solution for syncing only what's new or changed.

---

## The Solution

A fully automated pipeline that runs on a schedule, detects only new or modified files in Google Drive, processes them, and uploads them to Pinecone. Files that were already processed are skipped entirely.

---

## System Architecture

![Architecture](./architecture.png)

---

## How It Works

```
Schedule Trigger (runs automatically)
              ↓
    Fetch files from Google Drive
              ↓
    Compare with Google Sheets log
    (custom JavaScript comparison)
              ↓
         New file?
        /         \
      YES           NO
       ↓             ↓
   Process         Skip
   the file
       ↓
   Download file
       ↓
   Chunk text
   (Recursive Character Text Splitter)
       ↓
   Generate embeddings
   (Gemini Embeddings)
       ↓
   Upsert to Pinecone
       ↓
   Log file in Google Sheets
```

---

## Components

### 1. Smart Filtering (The Core Logic)

![Filtering](./screenshots/01_filtering.png)

A custom JavaScript function compares the current files in Google Drive against a log stored in Google Sheets. Only files that are new or modified pass through to processing. Everything else is skipped.

This eliminates 100% of redundant API calls.

---

### 2. File Processing Pipeline

![Processing](./screenshots/02_processing.png)

For each new file:
- Downloads the file from Google Drive
- Splits content into chunks using Recursive Character Text Splitter
- Converts chunks to vectors using Gemini Embeddings
- Upserts vectors into Pinecone with metadata

---

### 3. RAG Agent

![RAG Agent](./screenshots/03_rag_agent.png)

An AI agent (Ollama + Groq) connected to the Pinecone vector store. When a user sends a message, the agent searches the vector store and returns an answer based on the latest uploaded documents.

---

### 4. Google Sheets Log

Tracks every file that has been processed:

| File Name | File ID | Last Modified | Uploaded |
|-----------|---------|---------------|----------|
| product_catalog.pdf | ... | 2024-01-15 | ✓ |
| shipping_policy.pdf | ... | 2024-01-20 | ✓ |

---

## Tech Stack

| Category | Tools |
|----------|-------|
| Automation | n8n (Self-Hosted) |
| AI Models | Gemini Embeddings, Ollama, Groq |
| Vector Database | Pinecone |
| Storage | Google Drive API, Google Sheets |
| Language | JavaScript |
| Infrastructure | Docker, VPS, SSL |

---

## Repository Structure

```
rag-sync-system/
├── screenshots/
│   ├── 01_filtering.png
│   ├── 02_processing.png
│   └── 03_rag_agent.png
├── architecture.png
└── README.md
```

---

## Note

Workflow logic, JavaScript deduplication code, and credentials are kept private. This repository covers architecture and design decisions only.

---

## Contact

- fares.amr@gmail.com
- linkedin.com/in/fares-amr
- github.com/fares-amr
