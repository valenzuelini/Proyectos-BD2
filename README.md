# Proyectos-BD2

Proyecto final de **Bases de Datos II** (UNAH, III PAC 2026): búsqueda semántica sobre una base de datos vectorial, en el dominio **Finanzas**.

La base principal es **pgvector** (PostgreSQL). **Qdrant** y **ChromaDB** se comparan con ella en el informe. La búsqueda usa solo **similitud del coseno**.

## Datos

| Documento | Categoría | Fragmentos |
| --- | --- | --- |
| Decálogo de derechos y obligaciones de los usuarios financieros (CNBS) | `proteccion_usuario_financiero` | 20 |
| Resolución GEE No. 500/2024, La Gaceta 36,610 (CNBS) | `normativa` | 69 |
| **Total** | | **89** |

Cada fragmento tiene los metadatos `documento`, `categoria`, `fuente`, `seccion`, `numero_item`, `chunk_index` y `contenido`. La categoría es el nombre de la subcarpeta donde está el PDF en `data/raw/`.

Los embeddings se generan con `paraphrase-multilingual-MiniLM-L12-v2` (384 dimensiones) y se guardan en `data/processed/embeddings.npy`, con forma `(89, 384)`.

## Estructura

```
data/raw/<categoria>/   PDFs originales (la subcarpeta es la categoría)
data/processed/         chunks_*.csv, embeddings.npy y tiempos_*.csv
notebooks/              limpieza, embeddings, carga/búsqueda y evaluación
src/                    código del equipo
docs/                   investigación, diseño y conclusiones
evidencias/             capturas de creación, carga y consultas
```

## Notebooks

Se ejecutan en este orden:

| Notebook | Qué hace |
| --- | --- |
| `01.1_limpieza_chunking_decalogo.ipynb` | Limpia y fragmenta el Decálogo, y genera `chunks_decalogo.csv` |
| `01.2_limpieza_chunking_Gaceta36610.ipynb` | Limpia y fragmenta la Resolución GEE 500 por artículos, y genera `chunks_gaceta_gee500.csv` |
| `02_embeddings.ipynb` | Une todos los `chunks_*.csv` y genera `embeddings.npy` |
| `03_carga_y_busqueda.ipynb` | Carga en pgvector, crea el índice HNSW, consulta y mide tiempos |
| `03_carga_y_busqueda_qdrant.ipynb` | Lo mismo con Qdrant |
| `03_carga_y_busqueda_chromadb.ipynb` | Lo mismo con ChromaDB |
| `04_evaluacion.ipynb` | Compara los tiempos de `tiempos_*.csv` |

Cada PDF nuevo necesita su propio notebook de limpieza, que deja un `chunks_<nombre>.csv` en `data/processed/`. El notebook 02 los lee todos automáticamente.

## Uso

```powershell
docker compose up -d
pip install -r requirements.txt
```

Servicios: pgvector `localhost:5432` (base `vectores`), Qdrant `localhost:6333`, Chroma `localhost:8000`.

Después ejecuta los notebooks en orden. Si agregas o cambias fragmentos, vuelve a correr el 02 y los de carga.

## Metodología de evaluación

- Diez preguntas que cubren los dos documentos, con K = 3, 5 y 10.
- Tres consultas de calentamiento que no se miden; los vectores de las preguntas se calculan de antemano y solo se cronometra la consulta con `time.perf_counter()`.
- Índice HNSW con `m = 16` y `ef_construction = 64`.
- Los tiempos comparativos de las tres bases se midieron con los 20 fragmentos del Decálogo; con los 89 fragmentos solo se volvió a medir pgvector.
