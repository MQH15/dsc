# Proyecto Integrador - Laboratorio 3

**Programa:** Data Scientist Cognitive  
**Proyecto:** Asesor virtual de crédito hipotecario para vivienda

Este laboratorio incorpora recuperación aumentada por generación (RAG) al agente construido en el Laboratorio 2. El agente utiliza documentos sintéticos de Banco Horizonte, embeddings de Azure OpenAI y ChromaDB para responder a partir de información recuperada, conservar su memoria de corto plazo y evitar inventar condiciones o cifras.

Los datos son completamente sintéticos y están destinados únicamente a prácticas académicas.

## Contenido del laboratorio

- Configuración del LLM y del modelo de embeddings con Microsoft Agent Framework.
- Reutilización del agente hipotecario, su system prompt, memoria y compactación.
- Preparación de conocimiento sintético y división por secciones.
- Generación de embeddings con `text-embedding-3-small`.
- Indexación y recuperación semántica con ChromaDB.
- Aplicación opcional de filtros por metadatos.
- Integración del contexto recuperado en las respuestas del agente.
- Validación del RAG y propuestas de Tools y Agent as a Tool.

## Archivos principales

```text
lab03/
├── lab_03_00_proyecto_integrador.ipynb  # Desarrollo completo del laboratorio
├── lab_03_00_proyecto_integrador.html   # Versión exportada del notebook
├── catalogo_productos_bancarios_rag.json
│                                         # Catálogo sintético de productos bancarios
├── documentos_hipotecario_sinteticos.json
│                                         # Guía y FAQ hipotecaria sintéticas
├── chroma_proyecto/
│                                         # Base persistente de ChromaDB
├── .env.example                         # Plantilla de variables de entorno
├── requirements.txt
└── README.md
```

## Tecnologías

- Python y Jupyter Notebook
- Microsoft Agent Framework
- Azure OpenAI / Microsoft Foundry
- Microsoft Entra ID con `DefaultAzureCredential`
- Azure OpenAI Embeddings
- ChromaDB persistente
- `python-dotenv`

## Requisitos previos

- Python 3.11 o superior
- Un modelo de chat y un modelo de embeddings desplegados en Microsoft Foundry
- Acceso a Azure mediante Microsoft Entra ID
- Azure CLI
- Jupyter Lab o Jupyter Notebook compatible con VS Code

## Configuración

### 1. Crear el entorno virtual

Desde la carpeta `lab03`:

```powers
python -m venv venv
venv\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
python -m ipykernel install --user --name lab03-rag
```

Para entornos compatibles con Bash:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m ipykernel install --user --name lab03-rag
```

### 2. Configurar variables de entorno

Copie la plantilla y complete los valores disponibles en su entorno:

```powershell
copy .env.example .env
```

El notebook requiere:

```dotenv
FOUNDRY_MODEL_ENDPOINT="https://<recurso>.services.ai.azure.com/openai/v1"
FOUNDRY_MODEL_DEPLOYMENT="<deployment-del-modelo-de-chat>"
FOUNDRY_EMBEDDING_DEPLOYMENT="<deployment-del-modelo-de-embeddings>"
```

No es necesario agregar API keys. La autenticación se realiza mediante `DefaultAzureCredential`.

### 3. Iniciar sesión en Azure

```bash
az login
```

### 4. Ejecutar el notebook

Abra `lab_03_00_proyecto_integrador.ipynb` en Jupyter o VS Code, seleccione el kernel `lab03-rag` y ejecute las celdas en orden.

> El notebook usa `await` en el nivel superior, por lo que debe ejecutarse como notebook y no como un archivo Python convencional.

## Arquitectura RAG

```text
Pregunta del usuario
        ↓
Embedding con text-embedding-3-small
        ↓
Consulta semántica en ChromaDB
        ↓
Fragmentos recuperados + metadatos
        ↓
Contexto añadido al prompt del agente
        ↓
Respuesta fundamentada y con fuente
```

El índice utiliza la métrica de distancia coseno. La colección persistente se llama `conocimiento_hipotecario` y contiene los chunks, sus embeddings y metadatos.

## Diseño del conocimiento

Los documentos se dividen por estructura, usando una sección por chunk. Los fragmentos incluyen:

- Un encabezado con el título del documento y la sección.
- El contenido textual de la sección.
- Metadatos como categoría, fuente, título, producto y identificador del documento.

La categoría es el metadato principal para búsquedas filtradas. Los metadatos de fuente, título y sección permiten mostrar procedencia al agente.

Ejemplo de categorías:

- `requisitos`
- `proceso`
- `preguntas_frecuentes`

## Uso del agente

El agente se especializa exclusivamente en crédito hipotecario para vivienda. Ante consultas compatibles:

- Recupera hasta tres fragmentos relevantes.
- Puede limitar la recuperación a una categoría.
- Usa únicamente el contexto recuperado para datos específicos.
- Declara si el contexto no contiene la respuesta.
- Menciona la fuente utilizada.
- Conserva datos proporcionados por el usuario mediante memoria de corto plazo.

Ante otros productos, el agente deriva la consulta a la especialidad correspondiente. Ante temas ajenos a banca o finanzas, rechaza la consulta con cortesía.

## Validación

El notebook incluye pruebas de alcance y recuperación, entre ellas:

- Consultas sobre requisitos y documentos.
- Consultas sobre el proceso hipotecario.
- Cálculos referenciales con datos del usuario.
- Recuperación con y sin filtro.
- Una pregunta cuya respuesta no está disponible en el contexto.
- Consultas de otra especialidad bancaria y fuera del ámbito bancario.

La validación confirma que el agente conserva su alcance y que el prompt evita inventar cifras cuando el contexto no sustenta una respuesta.

## Tools previstas

El laboratorio plantea las siguientes Tools, aunque por ahora no las implementa:

| Tool | Uso previsto |
|---|---|
| `buscar_documentos_hipotecario` | Consultar ChromaDB según la pregunta y una categoría opcional |
| `calcular_cuota_inicial` | Calcular porcentajes y montos de cuota inicial |
| `evaluar_relacion_cuota_ingreso` | Comparar el endeudamiento total con el 40 % referencial |
| `verificar_documentos_pendientes` | Determinar qué documentos faltan según el tipo de ingreso |

`simular_cuota_mensual` no se propone todavía porque el conocimiento disponible no contiene una tasa ni un plazo aprobados.

## Seguridad y datos

- No incluya API keys ni secretos en el notebook.
- No suba el archivo `.env`.
- Mantenga los datos y documentos sintéticos como material académico.
- Los montos, tasas, plazos y umbrales de los documentos son referenciales y no representan condiciones reales de Banco Horizonte.
- Una aplicación real debería incorporar validación de entradas, trazabilidad de consultas y control de acceso a las fuentes.

## Ejecución limpia

Antes de ejecutar nuevamente, el notebook puede eliminar y recrear la colección para evitar mezclar chunks o embeddings generados con distintas configuraciones.

La opción `REINDEXAR = True` permite reconstruir el índice desde los datos actuales. Si se conserva el índice y no cambian los chunks ni el modelo de embeddings, puede establecerse en `False`.
