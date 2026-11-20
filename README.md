# Airflow + Pinecone: Medium Article Semantic Search

DATA 226 assignment (Week 13). An Airflow DAG, `Medium_to_Pinecone`, runs in Docker and builds a
semantic search index of Medium article titles in Pinecone.

## What the DAG does
`download_data` → `preprocess_data` → `generate_embeddings_and_upsert` → `test_search_query`,
with `create_pinecone_index` running alongside and feeding into the embedding step.

1. **download_data**: downloads the Medium dataset (about 2,500 rows) as a CSV.
2. **preprocess_data**: cleans titles and subtitles and builds the metadata and IDs.
3. **create_pinecone_index**: creates (or resets) the `semantic-search-fast` serverless index (384 dimensions).
4. **generate_embeddings_and_upsert**: embeds titles with a sentence-transformers MiniLM model and upserts them to Pinecone.
5. **test_search_query**: runs a sample semantic search against the index.


## Files
- `docker-compose.yaml`: Airflow 2.10.1 (LocalExecutor) with `sentence-transformers` and `pinecone` added to `_PIP_ADDITIONAL_REQUIREMENTS`.
- `dags/build_pinecone_search.py`: the DAG.

## How to run
1. `docker compose up -d` and wait until the `airflow` container is `healthy` (the first start installs packages and takes several minutes).
2. Open http://localhost:8082 (login `airflow` / `airflow`).
3. Create a Pinecone account, copy your API key, then add it in **Admin → Variables** as `pinecone_api_key`. The key is stored in Airflow, never in this repo.
4. Unpause and trigger `Medium_to_Pinecone`, and check that every task turns green.

