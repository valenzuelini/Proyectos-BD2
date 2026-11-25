# Proyectos-BD2
proyecto para bases de datos 2 sobre bases de datos vectoriales,embeddings ,busqueda semantica 


## Estructura

```
data/raw/<categoria>/   PDFs originales (la subcarpeta es la categoria)
data/processed/         datos procesados (chunks.csv)
src/                    codigo del equipo (procesamiento, carga a las bases, evaluacion)
notebooks/              notebook de embeddings y busqueda
docs/                   investigacion, diseno y conclusiones
evidencias/             capturas de creacion, carga y consultas
```

## Uso

```powershell
docker compose up -d
pip install -r requirements.txt
```

Servicios: pgvector `localhost:5432` (db `vectores`), Qdrant `localhost:6333`, Chroma `localhost:8000`.
