# Climate-Smart Agriculture AI Assistant

A question-answering system built on top of FAO, IPCC, CGIAR, and World Bank research documents. Ask it anything about climate-smart agriculture, food security, or smallholder farming and it returns answers drawn directly from the source material — not from model memory.

Built this because I kept running into the same problem: LLMs give confident, plausible-sounding answers about agricultural topics that are factually wrong or outdated. This system fixes that by searching real documents first, then generating answers from what it actually finds.

---

## What it does

You type a question. The system searches through 1,687 pages of agricultural research, pulls the three most relevant passages, feeds them to a language model alongside your question, and returns an answer grounded in those passages. Every answer traces back to a specific document and page number.

---

## How it works

```
Your question
      ↓
Converted to a 384-dimensional vector (numerical representation of meaning)
      ↓
FAISS searches 16,795 stored vectors for the closest matches
      ↓
Top 3 most relevant passages retrieved with their source citations
      ↓
Passages + question sent to Flan-T5-Large
      ↓
Answer generated from the retrieved content only
```

The key is that the model is explicitly instructed to answer only from what was retrieved. It cannot fall back on training memory. This eliminates hallucination for domain-specific questions.

---

## Knowledge base

| Document | Publisher | Pages |
|---|---|---|
| Climate Smart Agriculture Sourcebook | FAO | 629 |
| Special Report on Climate Change and Land (Full Report) | IPCC | 908 |
| SRCCL Summary for Policymakers | IPCC | 36 |
| Climate-Related Development Finance | FAO | 28 |
| Framework for Transforming Urban Agrifood Systems | FAO | 32 |
| Integrating Agriculture in National Adaptation Plans | FAO | 16 |
| Advancing Climate-Smart Education in Africa | CGIAR | 4 |
| Nigeria Food Smart Country Diagnostic | World Bank | 34 |

1,687 pages total → 16,795 searchable chunks

---

## Stack

| Component | Tool |
|---|---|
| Document loading | LangChain PyPDFLoader |
| Text chunking | RecursiveCharacterTextSplitter |
| Embeddings | sentence-transformers/all-MiniLM-L6-v2 |
| Vector database | FAISS (Facebook AI Similarity Search) |
| Language model | google/flan-t5-large |
| Chain orchestration | LangChain LCEL |
| Interface | Gradio |

---

## Running it

```bash
git clone https://github.com/PraiseO2/climate-rag-agriculture.git
cd climate-rag-agriculture
pip install -r requirements.txt
```

Add your PDFs to the `data/` folder, then:

```bash
python app.py
```

First run builds the vector store (~2 minutes). Every run after loads it from disk in under 30 seconds.

---

## Things I want to improve

- Swap Flan-T5 for Mistral-7B-Instruct for longer, more detailed answers
- Add source citation display in the UI so users can read the original passages
- Conversational memory so follow-up questions work naturally
- Multilingual support — Yoruba, Hausa, Swahili — for rural accessibility
- Deploy to HuggingFace Spaces for permanent hosting

---

## Background

I spent a year doing pathology research on Bambara groundnut resistance breeding. The gap between what academic research knows and what farmers can actually access is embarrassing. This is one attempt to close it.

This project is part of a larger system I'm building — a climate-smart agriculture assistant that combines document intelligence, crop disease detection from leaf images, and commodity price forecasting into one tool for smallholder farmers.

---


