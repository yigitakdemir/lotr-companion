# Lord of the Rings Companion
### A RAG-Powered Interactive Web Application for Middle-Earth

**Course:** MPCS 53416 Generative AI — University of Chicago  
**Author:** Yigit Akdemir  
**Stack:** Python · LangChain · FAISS · OpenAI GPT · Streamlit

---

## Overview

The Lord of the Rings Companion is a Retrieval-Augmented Generation (RAG) application built with Streamlit and LangChain. It uses FAISS (Facebook AI Similarity Search) to retrieve context from the full-text content of J.R.R. Tolkien's The Lord of the Rings trilogy, stored as PDFs in a `docs` folder. This context is then used to support interview-like conversations with main LOTR characters, answer detailed lore questions, generate quizzes, and provide insights about Middle-Earth.

The application maintains **two separate FAISS indices** — one for general lore queries and one for character-specific in-character responses — allowing highly granular, context-aware answers to even the most obscure detail-level questions from the books.

---

## Features

- **Interview a Character:** Ask specific questions to a selected main character in LOTR, where the character can elaborate on their actions down to the very minor details in the books. They can also talk about their aims and thoughts about different events and about other characters. They can even tell the user exactly what they said to others at various times in the LOTR timeframe. The conversation history can be seen on the page, and cleared when desired.

- **Talk with an Expert:** Ask questions about anything related to the three LOTR books, including very minor details. The page is able to answer anything as long as it can be deduced or inferred from the content of the books.

- **Explore a Character or Artifact:** Learn about the history and significance of LOTR characters and artifacts. For characters, the page also describes their powers; for artifacts, it describes the characters associated with them.

- **Test Your LOTR Knowledge:** A 6-question quiz about the events in the LOTR books, designed for fans who want to be tested with advanced-level, detail-oriented questions. At the end of the quiz, the user can see their total score along with the result for each question.

---

## Technical Architecture

### RAG Pipeline

| Component | Implementation |
|-----------|---------------|
| **Document Loader** | `PyPDFLoader` via LangChain — loads PDFs from `docs/` folder |
| **Text Splitting** | `RecursiveCharacterTextSplitter` — splits trilogy into overlapping chunks for FAISS embedding |
| **Embeddings** | `OpenAIEmbeddings` — generates dense vector representations of text chunks |
| **Vector Store** | **FAISS** — two separate indices (general lore + character-specific) for targeted retrieval |
| **Prompt Templates** | Custom `PromptTemplate` per page type (character interview, expert Q&A, quiz, artifact explorer) |
| **Context Assembly** | Top-k most relevant FAISS chunks assembled into structured context window |
| **LLM** | OpenAI GPT via `LangChain` chains — generates grounded responses from context |
| **Frontend** | Streamlit with custom CSS, Middle-Earth-inspired theming, background images and character visuals |

### Why Two FAISS Indices?
The dual-index architecture separates general lore retrieval (for the Expert and Artifact pages) from character-specific retrieval (for the Interview page). This allows character responses to stay contextually grounded in that character's specific dialogue, actions, and perspective — rather than pulling broadly from the entire trilogy. The result is highly specific, in-character answers even to obscure queries like *"Have you ever eaten an apple during your journey to Mordor?"*

---

## Installation

The following libraries and modules are imported in the source file:

```python
from langchain_community.document_loaders import DirectoryLoader
from langchain_community.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings
from langchain.prompts import PromptTemplate
from langchain.chains.question_answering import load_qa_chain
from langchain_openai import OpenAI
from dotenv import load_dotenv
import streamlit as st
import random
```

Install dependencies:

```bash
pip install langchain langchain-community langchain-openai faiss-cpu streamlit openai python-dotenv pypdf
```

---

## Setup

### 1. Set Up Environment Variables

Create a `.env` file in the project root directory and add the following:

```
OPENAI_API_KEY=<your_openai_api_key>
```

### 2. Configure Streamlit Theming

Ensure you have a `.streamlit/config.toml` file with the following for custom Middle-Earth theming:

```toml
[theme]
base = "dark"
primaryColor = "#F6CF33"
backgroundColor = "black"
secondaryBackgroundColor = "#2E2E2D"
textColor = "white"
```

### 3. Add the Book PDFs

Place the PDFs of **The Fellowship of the Ring**, **The Two Towers**, and **The Return of the King** in a folder named `docs` in the root directory. The PDFs are not included in this repository due to copyright — any standard edition of the trilogy in PDF format will work.

```
docs/
├── fellowship_of_the_ring.pdf
├── the_two_towers.pdf
└── return_of_the_king.pdf
```

### 4. Run the Application

```bash
py -m streamlit run .\lotr_companion.py
```

The application will start in your default web browser. The initial loading of books into FAISS will take approximately **5 minutes** on first run.

---

## Usage

The app is divided into four main pages, accessible via a sidebar:

- **Interview a Character**
- **Talk with an Expert**
- **Explore a Character or Artifact**
- **Test Your LOTR Knowledge**

---

## Interactive Styling

- Themed using custom CSS for a Middle-Earth-inspired look
- Background images, fonts, and character images enhance user experience
- Dark theme with gold accent color (`#F6CF33`) throughout

---

## Troubleshooting

- Ensure the book PDFs are placed in the correct `docs/` folder
- Verify your API key is correctly set in the `.env` file
- Check for missing dependencies and install them using `pip install`
- Ensure Streamlit theming is applied through the `.streamlit/config.toml` file

---

## Future Improvements

- Add other books from Tolkien's Legendarium such as *The Hobbit* and *The Silmarillion*
- Add persistent memory functionality when conversing with a character or expert across sessions
- Deploy the application publicly, with FAISS indices pre-hosted to eliminate the 5-minute loading time on first run
- Expand character roster beyond the main cast
