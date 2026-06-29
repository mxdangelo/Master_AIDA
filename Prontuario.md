---
date: 2026-06-13
tags: [prontuario]
status: active
---

# Prontuario — Master AIDA

Quick-reference operativo: **se devi fare X, cosa usi e dove approfondisci.** Non è il sommario degli appunti — è il banco degli attrezzi. La mappa completa delle note è in fondo.

## 🧭 Quale scegliere

### Che database mi serve?

| Se l'esigenza è… | Usa | Esempi | Approfondisci |
|---|---|---|---|
| transazioni, struttura fissa, integrità | **Relazionale** (SQL) | PostgreSQL, MySQL | [[Relazioni]] |
| accesso a chiave, cache, sessioni | **Key-Value** | Redis, DynamoDB | [[(non solo) Relazioni]] |
| documenti a schema flessibile | **Documentale** | MongoDB | [[MONGO DB]] |
| analitica, aggregazioni su colonne | **Columnar** | Cassandra | [[(non solo) Relazioni]] |
| relazioni fitte, traversal | **Grafo** | Neo4j | [[Neo4j]] |
| metriche nel tempo, IoT | **Time-series** | InfluxDB | [[(non solo) Relazioni]] |
| similarità, RAG, AI | **Vector** | Pinecone, Chroma | [[(non solo) Relazioni]] |

> [!tip] Regola d'oro
> Scalabilità NoSQL = **scale-out** (più nodi), non scale-up. **ACID** (consistenza forte, relazionali) vs **eventual consistency** (NoSQL, performance e scala).

### Embedding o referencing? (MongoDB)

> [!question] La domanda chiave
> *"How often do I read these data together?"* — **spesso insieme** → embedding · **aggiornato a parte / many-to-many / cresce senza limite (>16 MB)** → referencing. → [[MONGO DB]]

### ETL o ELT?

- **ETL** — trasforma *prima* di caricare (warehouse classico, schema-on-write).
- **ELT** — carica il grezzo, trasforma *dentro* (lakehouse, big data, schema-on-read).
- Pipeline moderna a strati: **Bronze** (grezzo) → **Silver** (pulito) → **Gold** (curato per BI/ML). → [[ETL]]

### Warehouse, Lake o Lakehouse?

| | Cosa | Quando |
|---|---|---|
| **Warehouse** | strutturato, schema-on-write | BI, reporting, dati puliti |
| **Lake** | grezzo, schema-on-read | data science, ML, esplorazione |
| **Lakehouse** | tabelle ACID sul lake (Delta Lake) | una copia per BI *e* ML |

→ [[ETL]] · [[Cloud computing]]

### Scraping: API, selettori o LLM?

| Approccio | Quando | Costo |
|---|---|---|
| **API ufficiale** | se esiste, *sempre* prima scelta | basso |
| **Selector** (BeautifulSoup, Scrapy) | pagine stabili e strutturate, a volume | basso, ma fragile ai redesign |
| **LLM** (GPT/Gemini/Claude) | sorgenti messy, variabili, non strutturate | token + latenza; valida contro schema |

> [!tip] In pratica
> Ibrido: scraper classico per *fetch + clean*, LLM per *interpretare*. → [[Data Ingestion]]

### Quale JOIN?

| Voglio… | JOIN |
|---|---|
| solo le righe che combaciano | `INNER JOIN` |
| tutte le A (+ match) | `LEFT JOIN` |
| tutte le B (+ match) | `RIGHT JOIN` |
| tutte le A e le B | `FULL OUTER` (in MySQL/SQLite: `LEFT … UNION RIGHT …`) |

## ⚡ Operazioni comuni

### SQL
```sql
-- join + filtro + ordina
SELECT a.x, b.y FROM a JOIN b ON a.id = b.id WHERE b.z > 10 ORDER BY a.x;
-- aggregazione per gruppo
SELECT store, SUM(importo) FROM vendite GROUP BY store HAVING SUM(importo) > 1000;
-- subquery nella FROM (indicatori a granularità diversa)
SELECT f.tot/h.tot FROM (SELECT … GROUP BY …) AS f, (SELECT … GROUP BY …) AS h WHERE …;
```
> [!warning] Attenzione
> Il **JOIN precede l'aggregazione** → indicatori a granularità diversa vanno aggregati *prima* (subquery/vista/materializzazione). `DROP TABLE/DATABASE` **non ha undo**.

### Cypher (Neo4j)
```cypher
MATCH (p:Person)-[:INTERESTED_IN]->(s:Skill) RETURN p.name, s.name   // pattern
MERGE (p:Person {name:"X"})                                          // idempotente (≠ CREATE)
MATCH (p) OPTIONAL MATCH (p)-[:WORKS_FOR]->(c) RETURN p, c           // ≈ LEFT JOIN
```

### Python — pandas · PySpark · PyMongo · scraping
Snippet completi: [[Python|pandas]] · [[Spark#In pratica (PySpark)|PySpark]] · [[MONGO DB#In pratica (PyMongo)|PyMongo]] · [[Data Ingestion#In pratica (requests + BeautifulSoup)|scraping]].
```python
df = pd.read_csv("f.csv").groupby("k")["v"].sum()             # pandas (in-memory → RAM)
df.where(F.col("v") > 100).groupBy("k").agg(F.sum("v"))       # PySpark (lazy → show/count/collect)
col.find({"prezzo": {"$gt": 10}}, {"nome": 1})                # PyMongo (filtro + projection)
BeautifulSoup(requests.get(url).text, "html.parser").select("h2.title")  # scraping
glob.glob('**/*.csv', recursive=True)                         # tanti file insieme
```

### Dati mancanti
`case deletion` (pochi casi / campo chiave) · `imputation` (regressione, tree) · `special value` · `do nothing` (algoritmi robusti). → prima **interpreta** il mancante (omesso? non esiste? non raccolto? errore?). → [[Data Quality]]

## 🔎 Lookup rapidi

> [!info] Acronimi
> **ETL** Extract-Transform-Load · **OLTP** transaction processing (operativo) · **OLAP** analytical processing (cubo) · **4V+1** Volume, Velocità, Varietà, Veracity → **Value** · **ACID** Atomicity, Consistency, Isolation, Durability · **CAP** Consistency, Availability, Partition tolerance (ne scegli 2) · **IaaS/PaaS/SaaS** infrastruttura / piattaforma / software.

> [!info] AWS — i mattoni (cosa sono)
> **Bucket** (S3) = contenitore di oggetti (no file system) · **EC2** = macchine virtuali (es. *m5.xlarge*) · **Cluster** (EMR) = gruppo di EC2: **Primary** coordina, **Core** dati+calcolo, **Task** solo calcolo · **IAM** = i permessi (un *ruolo* autorizza, es. il cluster a leggere il bucket) · **VPC/subnet** = la rete (Studio nella stessa per raggiungere il cluster) · ⚠️ **termina il cluster** a fine sessione (costa a tempo). → [[Cloud computing]] · [[Spark#Su AWS — EMR (il lab)|setup EMR]]

**Regole e formule**
- `N tabelle → N-1 JOIN`. Molti-a-molti → **tabella ponte**.
- **PK**: niente duplicati né `NULL`. **FK**: duplicati ammessi.
- Ordine esecuzione SQL: `FROM/JOIN → WHERE → GROUP BY → aggregazione → HAVING → ORDER BY → SELECT`.
- Normalizzazione in 3 mosse: chiave minima → ogni attributo dipende dall'**intera** chiave → niente dipendenze transitive.
- **CAP**: P di fatto obbligata → scegli **CP** (banca) o **AP** (disponibilità). Mongo preferisce CP.
- **Accuracy** = corretti / osservati (sintattica = nel dominio; semantica = è il valore vero).
- **PageRank**: importanza = somma di chi ti punta, pesata dalla loro (damping 0.85).
- CSS selector: spazio = annidamento, `>` = diretto, `.` = classe, `#` = id.

## 🎯 Principi (da tenere in tasca)

> [!quote] I tre che valgono sempre
> *Garbage in, garbage out* — l'analisi vale quanto i dati. · *All models are wrong, but some are useful.* · Il dato giusto all'**attante** giusto, nella forma giusta per l'interlocutore (KPI).

- Prima di tutto: **farsi dire bene qual è la domanda di business** — da lì prende senso il resto.
- Un buon processo dati è **idempotente**: rilanciarlo non duplica né corrompe (upsert, non append ciechi).
- Se c'è un'**API**, è meglio dello scraping.
- **Move computation to data**, non i dati al programma (Hadoop). Spark è più veloce perché lavora *in-memory* e *lazy*.

## 🗺️ Mappa delle note (per area)

- **Fondamentali** — [[Dati]] · [[Logica booleana]]
- **Relazionali** — [[Relazioni]] · [[Modello ER]] · [[Normalizzazione]] · [[SQL]]
- **NoSQL** — [[(non solo) Relazioni]] · [[Aggregate Oriented Model]] · [[MONGO DB]] · [[Graph databases]] · [[Neo4j]]
- **Big data** — [[Hadoop]] · [[Spark]]
- **Data engineering** — [[Data Ingestion]] · [[ETL]] · [[Data Quality]]
- **Analytics** — [[BI Architecture]] · [[Machine Learning]] · [[Regressione lineare]]
- **Cloud** — [[Cloud computing]]
- **Strumenti** — [[Python]]
