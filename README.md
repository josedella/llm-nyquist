# Technical Customer Support with RAG & Fine-Tuned LLaMA 3

[![Conference](https://img.shields.io/badge/FLAIRS-38-blue)](https://www.flairs-38.info/)
[![Paper Status](https://img.shields.io/badge/Status-Full_Paper-success)](https://journals.flvc.org/FLAIRS/article/view/138954/144106)

**Project Name:** llm-nyquist
**Author:** Jose Della Sala
---

## 📢 Publication
This project was presented as a **Full Paper** at **FLAIRS 38**.
* **📄 Read the Full Paper:** [Technical Customer Service Support with RAG Fine Tunned Llama 3](https://journals.flvc.org/FLAIRS/article/view/138954/144106)
* **📚 View the Full Issue:** [FLAIRS 38 Conference Issue](https://journals.flvc.org/FLAIRS/issue/view/6467)

---

## 📄 Abstract
This project explores the application of **Retrieval-Augmented Generation (RAG)** using a **fine-tuned LLaMA 3** model to enhance technical customer support workflows for Bogen’s **E7000 system**. By creating a custom dataset derived from Bogen’s technical documentation and leveraging a multi-agent system for dataset generation, this project develops an LLM capable of processing complex technical queries, identifying potential issues, and proposing precise troubleshooting tips.

## 🚀 Key Features
* **Domain-Specific Fine-Tuning:** Uses **LLaMA 3-8B-Instruct** fine-tuned on a custom dataset of Bogen E7000 technical manuals.
* **Retrieval-Augmented Generation (RAG):** Dynamically retrieves context from an external knowledge base to augment responses and reduce hallucinations.
* **Multi-Agent Pipeline:** Unique multi-agent system (Question Generator, Answerer, Evaluator) to streamline dataset creation and evaluation.
* **Parameter-Efficient Fine-Tuning (PEFT):** Implements **Q-LoRA** (Rank 32) for efficient model adaptation.

## 🏗️ System Architecture & Methodology

The project implements a structured pipeline consisting of data extraction, synthetic dataset generation, fine-tuning, and evaluation.

### 1. Multi-Agent Dataset Creation
To solve the challenge of scarce domain-specific data, a multi-agent system was developed to generate high-quality Q&A pairs from documentation:
* **Question Generator Agent:** Extracts relevant contexts from the knowledge base and generates concise, unique questions.
* **Question Answering Agent:** Formulates accurate answers based on the generated questions and context.
* **JSON Formatting:** Data is structured into `id`, `question`, `context`, and `answer` fields for training.

### 2. Fine-Tuning Pipeline
* **Base Model:** Meta-LLaMA-3-8B-Instruct.
* **Technique:** Supervised Fine-Tuning (SFT) using `SFTTrainer` from the Hugging Face `trl` library.
* **Optimization:** Q-LoRA (Quantized Low-Rank Adaptation) to reduce memory usage while maintaining performance.
* **Configuration:**
    * **Context Window:** Chunked to <2,048 tokens.
    * **Epochs:** Models trained for 1 and 3 epochs were compared, with **3 epochs** yielding the best stability and performance.

### 3. Evaluation System
An **Evaluation Agent** (powered by GPT-4) was designed to grade the model's performance:
* **Quantitative:** Tested against a 48-question battery derived from Bogen's final exams. The agent assigns a binary score (0/1) based on alignment with expected answers.
* **Qualitative:** "Human-in-the-loop" style testing with real-life customer service scenarios (e.g., "My DCS attached to the GA10PV is flashing blue...").

## 📊 Results

The fine-tuned model demonstrated significant improvements over the base model in domain-specific tasks:

| Model | Average Score (Quantitative) | Performance Increase |
| :--- | :--- | :--- |
| **Base LLaMA 3** | 53.14% | - |
| **Fine-Tuned (1 Epoch)** | 54.71% | +1.57% |
| **Fine-Tuned (3 Epochs)** | **60.57%** | **+7.43%** |

* **Qualitative Success:** The 3-epoch model correctly identified specific registration failures (e.g., DCS flashing blue) and recommended appropriate hardware configurations (e.g., VoIP speakers for classrooms) where the base model failed.

## 📂 Repository Structure
*Based on the methodology described in the proceedings:*

```text
├── data/
│   ├── raw_pdfs/           # Bogen E7000 Manuals & Guides
│   ├── processed_chunks/   # Text segmented for context windows
│   └── qa_dataset.json     # Generated Q&A pairs for fine-tuning
├── src/
│   ├── agents/
│   │   ├── question_generator.py  # Agent for creating Qs from context
│   │   ├── question_answerer.py   # Agent for creating As from Qs
│   │   └── evaluator.py           # Agent for grading model outputs
│   ├── training/
│   │   ├── data_extraction.py     # Script to chunk PDFs
│   │   └── fine_tune.py           # SFTTrainer & Q-LoRA implementation
│   └── rag/
│       └── retrieval.py           # Context retrieval logic
└── README.md
