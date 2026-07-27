# Supervisor IA RRHH — Sistema Multiagente con RAG (LangChain + Gemini)

Sistema conversacional de Recursos Humanos para **PATITO S.A.** que combina múltiples agentes especializados con **RAG (Retrieval-Augmented Generation)**, análisis multimodal de documentos y gestión de solicitudes, todo expuesto mediante una interfaz de chat web construida con **Gradio**.

El sistema no responde de forma genérica: cada agente consulta **únicamente su propia base documental** (Chroma + embeddings de Gemini), evitando alucinaciones y garantizando respuestas trazables.

---
## Intregrantes:
- Arevalo Monica
- Lopez Wladimir
- Muyulema Karla
---

## Características principales

- **Orquestador inteligente (Supervisor):** clasifica la intención del mensaje del usuario y decide qué agente(s) especializados deben intervenir, incluso si una sola consulta mezcla varios temas.
- **Agentes RAG independientes:**
  - **Beneficios y Compensaciones**
  - **Reglamento Interno**
  - **Reclutamiento y Onboarding**
- **Agente de Acción:** gestiona solicitudes operativas (vacaciones, inclusión de dependientes), valida datos, pide confirmación explícita antes de registrar y deja constancia en un archivo de registro.
- **Agente Multimodal:** analiza imágenes de documentos/cédulas, extrae datos estructurados (nombre, cédula, fecha de nacimiento, etc.) y los usa para completar solicitudes automáticamente.
- **Gestión de estado conversacional:** el sistema recuerda en qué paso del flujo está el usuario (esperando datos, esperando confirmación, esperando imagen, etc.) para sostener conversaciones de varios turnos.
- **Interfaz web con Gradio:** chat con carga de imágenes, botón de reinicio de conversación y estilos personalizados.

---

## Arquitectura

```
Usuario
  │
  ▼
Interfaz Gradio (responder)
  │
  ▼
Supervisor (orquestador + estado de conversación)
  │
  ├── clasificar_agentes()  → LLM decide qué agentes activar
  │
  ├── Agente Beneficios      ──┐
  ├── Agente Reglamento       ├── RAG independiente (Chroma + Gemini Embeddings)
  ├── Agente Reclutamiento   ──┘
  │
  ├── Agente Multimodal  → analiza imagen (cédula / documento)
  │
  └── Agente de Acción   → extraer_datos → validar_datos → completar_datos
                              → confirmar_registro → registrar_solicitud (tool)
  │
  ▼
integrar_respuestas() → respuesta final consolidada
```

**Stack técnico:**

| Componente        | Tecnología                                   |
|--------------------|----------------------------------------------|
| LLM                | Google Gemini (`gemini-2.0-flash`, multimodal)|
| Embeddings         | `models/text-embedding-004`                   |
| Orquestación       | LangChain                                     |
| Base vectorial     | ChromaDB                                      |
| Herramientas       | `langchain_core.tools`                        |
| Interfaz           | Gradio                                        |
| Lenguaje           | Python 3.11                                   |

---

## Estructura del proyecto

```
├── PROYECTO-RRHH.ipynb          # Notebook principal con todo el sistema
├── 5_RRHH/                       # Documentos fuente para el RAG (no incluidos en el repo)
│   ├── 01_Beneficios_Compensaciones.txt
│   ├── 02_Reglamento_Interno.txt
│   └── 03_Reclutamiento_Onboarding.txt
├── registro_solicitudes_rrhh.txt # Se genera automáticamente al registrar solicitudes
└── README.md
```

Los archivos `.txt` dentro de `5_RRHH/` **no están incluidos en el repositorio** por defecto (son datos internos de ejemplo de la empresa ficticia PATITO S.A.). Debes crearlos tú con el formato de secciones numeradas (`1. `, `2. `, `3. ` ...) para que el chunking funcione correctamente.

---

## Instalación y uso

### 1. Clonar el repositorio

```bash
git clone https://github.com/tu-usuario/patito-rrhh-supervisor-ia.git
cd patito-rrhh-supervisor-ia
```

### 2. Crear entorno e instalar dependencias

```bash
pip install langchain langchain-google-genai langchain-community langchain-chroma chromadb pillow pandas ipywidgets gradio
```

### 3. Configurar la API Key de Gemini

Obtén tu clave en [Google AI Studio](https://aistudio.google.com/apikey) y expórtala como variable de entorno (o ingrésala con `getpass` al ejecutar el notebook, nunca la dejes escrita en texto plano):

```bash
export GOOGLE_API_KEY="tu_clave_aqui"
```

### 4. Preparar la base de conocimiento

Crea la carpeta `5_RRHH/` junto al notebook y coloca dentro los 3 documentos `.txt` con secciones numeradas.

### 5. Ejecutar

Abre `PROYECTO-RRHH.ipynb` y ejecuta las celdas en orden (Kernel → Restart & Run All). La última celda lanza la interfaz de Gradio.

---
## Link Video Proyecto RRHH: https://youtu.be/0TB_wsTxQtk
---
## Agentes disponibles

| Agente | Función |
|---|---|
| `BENEFICIOS` | Seguro médico, dependientes, bonificaciones, compensaciones |
| `REGLAMENTO` | Jornada laboral, vacaciones, permisos, código de conducta |
| `RECLUTAMIENTO` | Proceso de selección, referidos, onboarding, plan 30-60-90 |
| `ACCION` | Registro de solicitudes (vacaciones, inclusión de dependientes) |
| `MULTIMODAL` | Análisis de imágenes de cédulas/documentos laborales |

---

## Roadmap / posibles mejoras

- [ ] Persistencia de conversaciones en base de datos en lugar de variable global en memoria.
- [ ] Autenticación de usuarios en la interfaz de Gradio.
- [ ] Panel de administración para revisar solicitudes registradas.
- [ ] Tests automatizados para los agentes RAG.
- [ ] Despliegue en la nube (Hugging Face Spaces / Cloud Run).

---

## Licencia

Este proyecto es un caso de estudio académico. Ajusta esta sección con la licencia que prefieras (MIT, Apache 2.0, etc.).

---


