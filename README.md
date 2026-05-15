# Beca 18 RAG Chatbot

Sistema de preguntas y respuestas basado en Retrieval-Augmented Generation (RAG) sobre la **Resolución Directoral Ejecutiva N.° 033-2026-MINEDU/VMGI-PRONABEC** (Reglamento oficial de Beca 18 y Becas Especiales — Convocatoria 2026).

El sistema responde **únicamente** con información contenida en el documento oficial. Si la información no está disponible, responde: *"The document does not contain information about this topic."*

---

## Pipeline

El pipeline extrae el texto del PDF de la resolución oficial página por página, añadiendo marcadores `[PAGE N]` para trazabilidad. El texto se tokeniza con `tiktoken` y se divide en chunks de 400 caracteres con overlap de 60 mediante `RecursiveCharacterTextSplitter`. Cada chunk se convierte en un vector de 768 dimensiones usando `gemini-embedding-001` con backoff automático para respetar los límites del free tier, y se almacena en una colección ChromaDB con distancia coseno de forma idempotente. Ante una pregunta, el sistema embebe la consulta, recupera los k fragmentos más relevantes y los pasa como contexto al modelo `gemini-2.5-flash`, que genera una respuesta fundamentada exclusivamente en el documento, citando números de página.

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

## Instalación y configuración

### Google Colab (recomendado)

1. Abre `notebooks/beca18_rag_chatbot.ipynb` en Google Colab.
2. Ve a **Secrets** (icono de llave en el panel izquierdo) y agrega tu clave:
   - Nombre: `GEMINI_API_KEY`
   - Valor: tu clave de la API de Gemini — obtenla en [Google AI Studio](https://aistudio.google.com/app/apikey)
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

## Cómo usar la interfaz de chat

Al ejecutar la celda del **Step 7**, aparecerá una interfaz interactiva en el notebook:

1. **Escribe tu pregunta** en el área de texto (por ejemplo: *"¿Cuáles son los requisitos para postular?"*).
2. **Ajusta el slider "Fragmentos"** para controlar cuántos fragmentos del documento se usan como contexto (por defecto: 5).
3. Haz clic en **"Consultar"** para obtener la respuesta.
4. Despliega **"Ver fragmentos fuente"** para revisar los fragmentos del documento recuperados, junto con su distancia semántica.
5. Usa **"Limpiar conversación"** para reiniciar el área de respuesta.

> El chatbot solo responde con información del reglamento oficial. Preguntas fuera del documento reciben la respuesta: *"The document does not contain information about this topic."*

---

## Requisitos

- Python 3.10+
- Clave de API de Gemini: [Google AI Studio](https://aistudio.google.com/app/apikey)

## Modelos utilizados

- **Embeddings:** `gemini-embedding-001` (768 dimensiones)
- **Generación:** `gemini-2.5-flash`
