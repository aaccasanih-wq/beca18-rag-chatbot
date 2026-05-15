# Beca 18 RAG Chatbot

Sistema de preguntas y respuestas basado en Retrieval-Augmented Generation (RAG) sobre la **Resolución Directoral Ejecutiva N.° 033-2026-MINEDU/VMGI-PRONABEC** (Reglamento oficial de Beca 18 y Becas Especiales — Convocatoria 2026).

El sistema responde **únicamente** con información contenida en el documento oficial. Si la información no está disponible, responde: *"The document does not contain information about this topic."*

---

## Pipeline

| Step | Descripción |
|------|-------------|
| 0 | Setup: instalación de dependencias y carga de API key |
| 1 | Extracción de texto del PDF con marcadores `[PAGE N]` |
| 2 | Tokenización (`tiktoken`) y chunking (`RecursiveCharacterTextSplitter`) |
| 3 | Embeddings con `gemini-embedding-001` (768 dims, backoff automático) |
| 4 | Indexación en ChromaDB con distancia coseno (idempotente) |
| 5 | Búsqueda semántica top-k |
| 6 | Generación fundamentada con `gemini-2.5-flash` |
| 7 | Interfaz de chat interactiva con `ipywidgets` |

---

## Estructura del repositorio

```
beca18-rag-chatbot/
├── data/
│   └── beca18_reglamento.pdf
├── notebooks/
│   └── beca18_rag_chatbot.ipynb
├── .env.example
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Cómo ejecutar

### Google Colab (recomendado)

1. Abre `notebooks/beca18_rag_chatbot.ipynb` en Google Colab.
2. Ve a **Secrets** (icono de llave en el panel izquierdo) y agrega tu clave:
   - Nombre: `GEMINI_API_KEY`
   - Valor: tu clave de la API de Gemini
3. Ejecuta todas las celdas en orden (`Runtime → Run all`).

El notebook clona el repositorio automáticamente para acceder al PDF.

### Local (VS Code / Jupyter)

```bash
git clone https://github.com/aaccasanih-wq/beca18-rag-chatbot.git
cd beca18-rag-chatbot
conda create -n beca18-rag python=3.10 -y
conda activate beca18-rag
pip install -r requirements.txt
cp .env.example .env   # agrega tu GEMINI_API_KEY en .env
```

Luego abre `notebooks/beca18_rag_chatbot.ipynb` y ejecuta todas las celdas.

---

## Requisitos

- Python 3.10+
- Clave de API de Gemini: [Google AI Studio](https://aistudio.google.com/app/apikey)

## Modelos utilizados

- **Embeddings:** `gemini-embedding-001` (768 dimensiones)
- **Generación:** `gemini-2.5-flash`
