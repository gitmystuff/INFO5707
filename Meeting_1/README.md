# Meeting 1 Starter — Getting Started

This repository contains the **starter code** for Week 1 of INFO 5707.  
You will build a PostgreSQL-backed REST API using **FastAPI** and explore it using **Swagger**.

Follow the steps below **in order**.

---

## 1. Unzip and open in VS Code

1. Download the starter zip provided by your instructor
2. Unzip it into a folder on your computer
3. Open **VS Code**
4. Select **File → Open Folder…**
5. Open the unzipped `rag_api_starter` folder

You should see this structure:

```

rag_api_starter/
_app/
_sql/
_.gitignore
_README.md
_requirements.txt

````

---

## 2. Create and activate a virtual environment

Open a terminal **inside VS Code**.

### Windows (PowerShell)
```bash
python -m venv .venv
.venv\Scripts\activate
````

### macOS / Linux

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Your terminal prompt should now show `(.venv)`.

---

## 3. Install Python dependencies

Make sure the virtual environment is active, then run:

```bash
pip install -r requirements.txt
```

This installs:

* FastAPI
* Uvicorn
* psycopg (PostgreSQL driver)

---

## 4. Create the PostgreSQL database

PostgreSQL must be installed and running locally.

### Option A: Using psql (recommended)

```bash
psql -U postgres
```

Inside the prompt:

```sql
CREATE DATABASE rag_lab;
\q
```

### Option B: Using pgAdmin

* Create a new database named `rag_lab`

---

## 5. Apply the database schema

From the project root folder:

```bash
psql -U postgres -d rag_lab -f sql/schema.sql
```

You should see no errors.

### Option B: Using pgAdmin (Load the schema)

1. Open **pgAdmin**
2. In the Browser panel, expand:
   - Servers  
   - Your PostgreSQL server  
   - Databases  
3. Click on the database named `rag_lab` to select it

---

### Open the Query Tool

4. With `rag_lab` selected, click:
   - **Tools → Query Tool**
   - (or right-click the database → **Query Tool**)

You should now see a SQL editor window.

---

### Load the schema file

5. In the Query Tool, click the **folder icon** (📂) or:
   - **File → Open File**
6. Navigate to your project folder and open:



This creates:

* `documents` table
* `chunks` table
* Primary keys, foreign keys, and indexes

---

### Populate the tables

```
INSERT INTO documents (title, source) VALUES
('RAG Overview', 'lecture_notes'),
('PostgreSQL Basics', 'lecture_notes'),
('FastAPI CRUD Service', 'lab'),
('SQL Analytics Patterns', 'lab'),
('Vector Databases Primer', 'reading'),
('Graph Databases and Neo4j', 'reading');
```

```
-- Chunks for RAG Overview
INSERT INTO chunks (document_id, content, chunk_index)
SELECT d.id, v.content, v.chunk_index
FROM documents d
JOIN (VALUES
  (0, 'RAG combines retrieval with generation so responses can cite external knowledge.'),
  (1, 'Typical pipeline: ingest, chunk, embed, retrieve top-k, then generate.'),
  (2, 'Stable IDs allow chunk text, embeddings, and graphs to reference the same document.'),
  (3, 'Chunking choices affect retrieval quality and downstream generation.')
) AS v(chunk_index, content) ON TRUE
WHERE d.title = 'RAG Overview';

-- Chunks for PostgreSQL Basics
INSERT INTO chunks (document_id, content, chunk_index)
SELECT d.id, v.content, v.chunk_index
FROM documents d
JOIN (VALUES
  (0, 'PostgreSQL is a relational database with transactions and constraints.'),
  (1, 'Primary keys uniquely identify rows; foreign keys enforce relationships.'),
  (2, 'Indexes speed reads but add overhead to writes.'),
  (3, 'Use EXPLAIN to understand query plans and performance.')
) AS v(chunk_index, content) ON TRUE
WHERE d.title = 'PostgreSQL Basics';

-- Chunks for FastAPI CRUD Service
INSERT INTO chunks (document_id, content, chunk_index)
SELECT d.id, v.content, v.chunk_index
FROM documents d
JOIN (VALUES
  (0, 'FastAPI builds APIs with automatic OpenAPI documentation.'),
  (1, 'CRUD maps HTTP methods to database operations.'),
  (2, 'Swagger UI enables interactive endpoint testing.'),
  (3, 'Return stable IDs from create endpoints for linking across systems.')
) AS v(chunk_index, content) ON TRUE
WHERE d.title = 'FastAPI CRUD Service';

-- Chunks for SQL Analytics Patterns
INSERT INTO chunks (document_id, content, chunk_index)
SELECT d.id, v.content, v.chunk_index
FROM documents d
JOIN (VALUES
  (0, 'Aggregations summarize rows into counts and totals.'),
  (1, 'GROUP BY creates groups; HAVING filters groups after aggregation.'),
  (2, 'Window functions compute analytics without collapsing rows.'),
  (3, 'CTEs improve readability for complex queries.')
) AS v(chunk_index, content) ON TRUE
WHERE d.title = 'SQL Analytics Patterns';

-- Chunks for Vector Databases Primer
INSERT INTO chunks (document_id, content, chunk_index)
SELECT d.id, v.content, v.chunk_index
FROM documents d
JOIN (VALUES
  (0, 'Vector databases store embeddings for similarity search.'),
  (1, 'Queries retrieve nearest vectors to approximate semantic similarity.'),
  (2, 'Metadata filters keep retrieval within scope.'),
  (3, 'Embeddings can live in a vector store while text remains in PostgreSQL.')
) AS v(chunk_index, content) ON TRUE
WHERE d.title = 'Vector Databases Primer';

-- Chunks for Graph Databases and Neo4j
INSERT INTO chunks (document_id, content, chunk_index)
SELECT d.id, v.content, v.chunk_index
FROM documents d
JOIN (VALUES
  (0, 'Graph databases store nodes and relationships.'),
  (1, 'Neo4j uses Cypher for pattern matching and path queries.'),
  (2, 'Graphs connect documents to entities, citations, and topics.'),
  (3, 'Keep canonical IDs in PostgreSQL and relationships in the graph.')
) AS v(chunk_index, content) ON TRUE
WHERE d.title = 'Graph Databases and Neo4j';
```

---

## 6. Configure database password

Open this file in VS Code:

```
app/db.py
```

Find this line:

```python
"password": "YOUR_PASSWORD",
```

Replace `YOUR_PASSWORD` with your **local PostgreSQL password**.

If your setup is different, you may also need to update:

* `user`
* `port`

---

## 7. Run the FastAPI server

From the project root:

```bash
uvicorn app.main:app --reload
```

You should see output indicating the server is running.

---

## 8. Open Swagger and test the API

Open a browser and go to:

```
http://127.0.0.1:8000/docs
```

Use Swagger to:

* Create a document (`POST /documents`)
* List documents (`GET /documents`)
* Get a document by ID
* Delete a document

Changes made through the API will be reflected in PostgreSQL.

---

## 9. Troubleshooting

### `psql` not found

* PostgreSQL is not on your PATH
* Use pgAdmin or reinstall PostgreSQL with PATH enabled

### Swagger shows errors

* Check that PostgreSQL is running
* Verify your password in `app/db.py`
* Confirm the database name is `rag_lab`

### VS Code can't find packages

* Make sure the `.venv` interpreter is selected:

  * Command Palette → Python: Select Interpreter → `.venv`

---

## 10. Reset and re-populate the tables

To **wipe all data and re-populate safely**, run this first:

```sql
TRUNCATE TABLE chunks, documents;
```

Then re-run your **INSERT statements** for `documents` and `chunks`.

---

## Why this works

* `TRUNCATE` removes **all rows** from the tables
* It is **much faster** than `DELETE`
* Table structure, constraints, and indexes remain intact
* Because `chunks` depends on `documents`, truncating **both at once** avoids foreign-key issues


