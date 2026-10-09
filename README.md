# 🛒 Asistente Técnico de Compatibilidad de Hardware (RAG) — Caso SP Digital

**Asignatura:** Ingeniería de Soluciones con IA (ISY0101)  
**Evaluación:** Evaluación Parcial N°1  
**Integrantes:** Aaron Gonzalez Rojas, Juan Montero Henao  

## 📌 Descripción del Proyecto
En este proyecto desarrollamos un prototipo de Asistente basado en LLM y RAG (Recuperación Aumentada por Generación) para el caso de estudio de la tienda SP Digital. El objetivo de nuestro trabajo es automatizar las respuestas a consultas técnicas sobre compatibilidad entre distintos componentes de PC (placas madre, procesadores, tarjetas de video, RAM y fuentes de poder).

Para evitar que el modelo invente datos (alucinaciones), implementamos un pipeline RAG híbrido que cruza la información recuperada de nuestro catálogo vectorial con un validador de reglas de negocio. Si el sistema detecta que falta información, deriva la consulta a un vendedor humano. También integramos LangSmith para tener trazabilidad de las ejecuciones y evaluar el rendimiento.

## 🏗️ Arquitectura que armamos

```text
[Consulta del Usuario]
       │
       ▼
[1. Búsqueda Vectorial (FAISS + Mistral Embeddings)]
       │
       ▼
[2. Generación de Respuesta (Ministral-8b via API)]
       │
       ▼
[3. Motor de Validación de Reglas de Negocio]
       ├── ▶ ¿Coincide con regla de compatibilidad? ──▶ [Respuesta Verificada]
       └── ▶ ¿Falta dato o no hay regla? ──▶ [Derivación a Vendedor Humano]
```

## 🛠️ Herramientas y Tecnologías
- **Lenguaje:** Python 3.10+
- **LLM:** `ministral-8b-latest` (usando la API de Mistral AI)
- **Buscador Vectorial:** FAISS (`faiss-cpu`) con embeddings `mistral-embed`
- **Monitoreo y Evaluación:** LangSmith (`langsmith`)
- **Entorno:** Jupyter Notebook / Visual Studio Code

## 🚀 Cómo ejecutar nuestro proyecto

### 1. Clonar el repositorio
```bash
git clone https://github.com/imQuasar/asistente-hardware-rag-sp-digital.git
cd asistente-hardware-rag-sp-digital
```

### 2. Crear entorno virtual (Recomendado)

En Windows:
```bash
python -m venv venv
.\venv\Scripts\activate
```

En Mac/Linux:
```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Instalar librerías
```bash
pip install openai python-dotenv faiss-cpu numpy langsmith
```

### 4. Configurar las API Keys (.env)
Dejamos un archivo `.env.example` de guía. Debes crear tu propio archivo `.env` en la misma carpeta con este formato:

```bash
LLM_API_KEY=mstrl_tu_api_key_de_mistral
LANGSMITH_API_KEY=lsv2_pt_tu_api_key_de_langsmith
LANGSMITH_PROJECT=SP-Digital-RAG-Evaluation
```

> ⚠️ **Nota de seguridad:** Pusimos el archivo `.env` en nuestro `.gitignore` para no subir las claves reales de Mistral y LangSmith a GitHub.

## 🧪 Ejecución paso a paso
Para probar el código, abre el archivo `proyecto.ipynb` en VS Code, selecciona el entorno de Python y ejecuta las celdas en orden (Run All). El cuaderno procesa lo siguiente:

1. Carga nuestro catálogo de prueba y las reglas de negocio.
2. Crea el índice FAISS y vectoriza los textos.
3. Define el pipeline RAG usando los decoradores de LangSmith.
4. Muestra un test de precisión local.
5. Sube la evaluación RAG formal a LangSmith.

## 📊 Evaluación con LangSmith
Como parte de los requerimientos, implementamos una evaluación de métricas RAG usando un dataset de prueba (`DATASET_EVALUACION`). Medimos tres cosas:

- **Context Recall:** Comprueba si FAISS logró encontrar los SKUs correctos para responder la pregunta.
- **Faithfulness (LLM-as-a-Judge):** Usamos el LLM para auditar si la respuesta final se basó 100% en el contexto o si inventó especificaciones.
- **Verdict Accuracy:** Mide si el sistema acertó en el veredicto esperado (COMPATIBLE, NO COMPATIBLE o DERIVAR).

Todo el registro de latencia, costo de tokens y gráficas de resultados quedó guardado en nuestro panel de LangSmith bajo el proyecto `SP-Digital-RAG-Evaluation`.

## 📁 Qué hay en este repo

```bash
├── proyecto.ipynb      # Cuaderno principal con todo nuestro codigo y resultados
├── .env.example        # Plantilla de variables de entorno
├── .gitignore          # Filtro para ignorar archivos basura o sensibles
└── README.md           # Este documento
``` 
