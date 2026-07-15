#  Real-time Multimodal Live RAG with Gemini 2.0

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?style=for-the-badge&logo=python&logoColor=white)
![Google Cloud](https://img.shields.io/badge/Google%20Cloud-Vertex%20AI-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)
![Gemini 2.0](https://img.shields.io/badge/Model-Gemini%202.0%20Flash%20Live-8A2BE2?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

A low-latency, real-time **Retrieval-Augmented Generation (RAG)** system powered by Google's **Vertex AI Gemini 2.0 Multimodal Live API**. Unlike traditional text-only RAG pipelines, this project leverages bidirectional WebSocket streaming to deliver **instantaneous, human-like voice audio responses** grounded in domain-specific PDF documents.

---

## 🌟 What This Project Does

Standard RAG architectures suffer from high latency when converting retrieved text into voice (requiring separate Speech-to-Text, LLM processing, and Text-to-Speech models). 

This project solves that by utilizing the **Gemini 2.0 Multimodal Live API**, establishing a persistent, streaming connection that enables:
1. **Zero-Hallucination Grounding:** Extracts, chunks, and embeds unstructured PDF data using `text-embedding-004` to build a semantic vector index.
2. **Real-time Native Audio Generation:** Bypasses traditional TTS pipelines by streaming native audio output directly from the Gemini 2.0 Flash model with sub-second latency.
3. **Asynchronous Architecture:** Built with Python's `asyncio` and `google-genai` SDK to handle concurrent I/O operations and non-blocking real-time voice synthesis.

### 🚲 The Sample Use Case: "Cymbal Bikes" Customer Support
To demonstrate practical real-world utility, the pipeline is configured as an automated 24/7 retail customer support agent for a fictional bicycle shop (**Cymbal Bikes**). It ingests real store policies and service menus to accurately answer complex customer inquiries—such as pricing for tune-ups or helmet return windows—in a natural voice.

---

## 🏗️ System Architecture

```
[ Unstructured Data ] ──> ( PDF Extraction & Chunking ) ──> [ Vertex AI Embeddings ]
                                                                      │
                                                                      ▼
[ User Query (Text/Voice) ] ──> ( Cosine Similarity Search ) ──> [ In-Memory Vector Index ]
                                                                      │
                                                                      ▼
[ Grounded Response ] <── ( Gemini 2.0 Live WebSocket ) <── [ Augmented Prompt + Context ]
  (Text & Native Audio)
```

### Key Components:
* **Ingestion & Indexing:** Extracts pages using `PyPDF2`, splits text into 512-character chunks, generates 768-dimensional embeddings via `text-embedding-004`, and indexes them in a structured `pandas`/`numpy` DataFrame.
* **Semantic Retrieval:** Uses Cosine Similarity (`scikit-learn`) to dynamically retrieve and rank the top-$k$ most relevant context chunks.
* **Live Multimodal Generation:** Connects to `gemini-2.0-flash-live-preview` via `LiveConnectConfig`, streaming PCM raw audio buffers dynamically converted into playable sound.

---

## 🚀 Getting Started

### 1. Prerequisites
* **Python 3.10+**
* An active **Google Cloud Platform (GCP)** project with the **Vertex AI API** enabled.
* Google Cloud SDK (`gcloud`) installed and configured.

### 2. Installation & Setup

Clone the repository and install the required dependencies:

```bash
git clone https://github.com/your-username/gemini-2.0-multimodal-live-rag.ext
cd gemini-2.0-multimodal-live-rag

# Create and activate a virtual environment (optional but recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts ctivate

# Install dependencies
pip install --upgrade google-genai PyPDF2 numpy pandas scikit-learn tenacity ipython jupyter
```

### 3. Google Cloud Authentication
If running locally or on a custom VM, authenticate your environment with GCP:

```bash
gcloud auth application-default login
gcloud config set project [YOUR_PROJECT_ID]
```
*(Note: If running in **Google Colab**, authentication is handled natively via `google.colab.auth.authenticate_user()` inside the notebook).*

---

## 📂 Project Structure

```text
├── documents/
│   ├── CymbalBikesReturnPolicy.pdf    # Sample knowledge base document 1
│   └── CymbalBikesServices.pdf        # Sample knowledge base document 2
├── realtime_multimodal_rag.ipynb      # Main interactive RAG & Live API notebook
├── requirements.txt                   # Project dependencies
├── .gitignore                         # Git ignore rules
└── README.md                          # Project documentation
```

---

## 💻 Usage & Demonstration

Launch Jupyter Notebook or open the project in Google Colab:

```bash
jupyter notebook realtime_multimodal_rag.ipynb
```

### Running the Continuous Voice Assistant
The notebook includes an asynchronous interactive loop that lets you converse with the grounded agent in real-time. Simply type your query in the terminal/cell input, and the model will speak the answer back to you:

```python
await continuous_audio_generation()
# Prompt: "What is the price of a basic tune-up at Cymbal Bikes?"
# Output: [Audio Stream Begins Playing Native Voice Response] -> "A basic tune-up costs $100."
```

---

## 🛠️ Tech Stack
* **LLM / Generative AI:** Google Vertex AI Gemini 2.0 Flash (`gemini-2.0-flash-live-preview-04-09`)
* **Embeddings:** Vertex AI Text Embeddings (`text-embedding-004`)
* **SDK:** Google GenAI Python SDK (`google-genai`)
* **Data Processing:** NumPy, Pandas, PyPDF2
* **Math & Similarity:** Scikit-learn (Cosine Similarity)
* **Resilience:** Tenacity (Exponential backoff & API quota retry logic)

---

## 📄 License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
