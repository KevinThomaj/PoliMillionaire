# PoliMillionaire: NLP Chatbot for "Who Wants to Be a Millionaire"

## 1. Explanation of the Assignment and Problems

This project is developed for the **Natural Language Processing (2025/26) Group Assignment** at Politecnico di Milano. The core objective is to develop an intelligent chatbot capable of autonomously playing an online trivia game called *"Who wants to be a PoliMillionaire?"*

### The Problem
The game presents a series of multiple-choice questions across diverse categories (e.g., Entertainment, History, Science, Math, Philosophy, News) with increasing difficulty levels. To succeed, the chatbot must address several key challenges:
- **Time Constraint**: Each question must be answered within a strict 30-second timeout.
- **Accuracy & Knowledge Retrieval**: Questions range from simple trivia to complex math problems requiring step-by-step reasoning.
- **Strict Execution Rules**: 
  - All language models must be run **locally** (no paid LLM API usage).
  - The team must utilize **open-weights models**.
  - Any external search APIs used for RAG must return raw, non-generated content (like HTML/PDF) rather than pre-processed LLM answers.
- **Diverse Modalities**: The game features both a text-based interface and an upcoming audio interface, requiring robust transcription and processing capabilities.

---

## 2. Explanations of Solutions and Approaches Applied

To tackle these challenges, our notebook (`polimillionaire_gerry_scotti.ipynb`) explores and benchmarks a comprehensive pipeline of Natural Language Processing techniques. Our methodology spans from simple prompting to complex multi-agent architectures:

### A. Zero-Shot and Few-Shot Baselines
We began by evaluating 13 different open-weights models (including Gemma, Llama 3, Qwen, Mistral, and Phi-4). 
- **Approach**: Models were tested off-the-shelf using zero-shot, few-shot, and Chain-of-Thought (CoT) prompting techniques. 
- **Goal**: Establish a baseline to understand which architectures naturally perform best on specific trivia categories and how sensitive they are to prompt engineering.

### B. Supervised Fine-Tuning (SFT) & Reinforcement Learning
To improve performance on complex reasoning tasks (specifically Mathematics), we applied fine-tuning:
- **SFT**: We used LoRA (Low-Rank Adaptation) to fine-tune models on the AQuA-RAT math Q&A dataset.
- **SFT + GRPO**: We followed up the supervised fine-tuning with a two-stage reinforcement learning pipeline (Group Relative Policy Optimization) to further optimize the model's decision-making policy.

### C. Retrieval-Augmented Generation (RAG) Architectures
Given the massive breadth of knowledge required, we implemented several RAG systems to ground the LLM's answers in factual external data:
- **OpenStax XML RAG**: Targeted at mathematical and scientific questions, this system retrieves relevant formulas and theories from open-source algebra, calculus, and statistics textbooks.
- **Agentic RAG (TriviaQA-RC)**: Implements dynamic category routing. Depending on the question category, it utilizes ChromaDB to search over specialized trivia evidence corpora.
- **Wikipedia Hybrid RAG**: A robust fallback system combining BM25 (keyword search) and semantic search over live Wikipedia pages to retrieve up-to-date factual information.

### D. Ensemble Methods
To mitigate individual model hallucinations and overconfidence, we combined multiple LLMs into a unified decision-making system:
- **Soft Voting & Asymmetric Hard Voting**: Aggregating the probability distributions or final choices of multiple distinct models to select the most reliable, statistically sound answer.
- **Judge/Debate Framework**: Using an Agentic AI approach where models debate the options and a "Judge" LLM evaluates the reasoning to make the final decision.

### E. Speech-to-Text (STT) Pipeline
To handle the game's audio interface (where questions are spoken rather than provided in text):
- **Approach**: We implemented an audio transcription pipeline using OpenAI's **Whisper** model. This ensures the system can reliably and quickly transcribe the spoken questions within the 30-second window before passing the text to the core LLM ensemble.
