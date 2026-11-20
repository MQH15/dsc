# Proyecto Integrador · Laboratorio 2: Construcción del agente

**Programa de Formación: Data Scientist Cognitive**

Primera versión de un agente especializado en **crédito hipotecario para vivienda** (Banco Horizonte), construido con
**Microsoft Agent Framework**. Incluye instrucciones (system prompt), pruebas de alcance, memoria de corto plazo mediante
sesión y una estrategia de compactación del historial.

## Contenido del repositorio

```
.
├── notebooks/
│   └── proyecto_integrador.ipynb   # Notebook del Laboratorio 2
├── .env.example                    # Plantilla de variables de entorno
├── .gitignore
├── requirements.txt
└── README.md
```

## Decisiones de diseño

| Tema | Decisión |
|---|---|
| Especialidad | Crédito hipotecario para vivienda. Deriva tarjetas, consumo, maquinaria y otros productos a "otro agente". |
| System prompt | Rol, objetivo, contexto, restricciones y formato de salida, más alcance explícito, clasificación interna (A/B/C) y 3 ejemplos few-shot. |
| Técnicas de prompting | Rol, prompt estructurado, restricciones de alcance, grounding anti-alucinación, clasificación interna, few-shot, control de formato y defensa básica ante manipulación. |
| Memoria de corto plazo | `InMemoryHistoryProvider` + sesión (`create_session()`), con `default_options={"store": False}`. |
| Interacciones a conservar | **10** (≈ 20 mensajes): cubre la recolección de datos de una consulta hipotecaria sin inflar tokens. |
| Compactación | **`SummarizationStrategy`**: conserva en un resumen los datos del cliente dados al inicio; mantiene las últimas 10 interacciones literales. |

Las tres preguntas del laboratorio están respondidas dentro del notebook (secciones 2A, 3 y 4).

## Requisitos previos

- Python 3.11 (el usado en el curso) o superior
- Un modelo desplegado en **Microsoft Foundry** y acceso con Microsoft Entra ID
- Azure CLI (`az login`)

## Puesta en marcha

```bash
# 1. Entorno virtual
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# 2. Dependencias
pip install -r requirements.txt  # si falla: pip install agent-framework --pre

# 3. Variables de entorno
cp .env.example .env             # Windows: copy .env.example .env
# edita .env con tu endpoint y deployment

# 4. Autenticación
az login

# 5. Abrir el notebook
jupyter lab notebooks/proyecto_integrador.ipynb
```

> El notebook usa `await` en el nivel superior, así que debe ejecutarse en Jupyter/VS Code (no como script `.py`).

## Cómo adaptar la especialidad

En la celda del system prompt edita `ESPECIALIDAD`, `TEMAS_PERMITIDOS` y `OTRAS_ESPECIALIDADES`.
Ajusta también los casos de `CASOS_ALCANCE` y los ejemplos few-shot del prompt.

## Cómo probar la compactación

1. Ejecuta la sección **4.1**: muestra el historial antes y después de compactar (valores pequeños: `target_count=4`, `threshold=2`).
2. Ejecuta la sección **4.2**: da datos al inicio, genera varias interacciones y vuelve a preguntar por esos datos.
3. La sección **4.3** crea el agente final con los valores definidos (`target_count=20`, `threshold=4`).

## Checklist del Laboratorio 2

- [ ] Validación de las actividades del Laboratorio 1
- [ ] Agente construido con Microsoft Agent Framework
- [ ] System prompt con rol, objetivo, contexto, restricciones y formato de salida
- [ ] Pruebas con consultas dentro y fuera del alcance
- [ ] Memoria de corto plazo funcionando mediante una sesión
- [ ] Número de interacciones definido y justificado
- [ ] Estrategia de compactación seleccionada, implementada y probada
- [ ] Las tres preguntas respondidas en el notebook

## Notas

- Este agente aún **no** consulta el catálogo oficial de productos (no conoce tasas ni requisitos exactos); por eso el prompt le
  indica no inventarlos. La recuperación de información (RAG) se incorporará en laboratorios posteriores.
- Ejecuta el notebook completo antes de subirlo para que las salidas queden como evidencia en el repositorio.
- No subas el archivo `.env`.
