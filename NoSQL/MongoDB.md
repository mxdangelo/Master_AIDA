---
date: 2026-05-11
tags: [database, nosql]
status: active
image: "[[assets/covers/nosql.svg]]"
area: nosql
---

# MongoDB

Il [[NoSQL|database documentale]] più usato. Progetto nato nel 2007, prima versione *production-ready* nel 2010. Multi-OS, cloud (AWS/Azure/GCP + **MongoDB Atlas**), driver per Java/Python/Scala/JS/C#, API REST. Memorizza documenti in una **gerarchia ad albero**; documenti simili stanno nella stessa collezione; **scale-out** su cluster. Rende al meglio quando l'interazione avviene con lo **stesso aggregato** ([[Aggregate Oriented Model]]).

## Terminologia: RDB ↔ Document

| RDBMS | MongoDB |
|---|---|
| Database | **Database** |
| Tabella / vista | **Collection** |
| Riga (record) | **Document** |
| Colonna (campo) | **Field** |

Ogni istanza MongoDB ha più database (≈ *schema*); ogni database più collezioni (≈ *tabella*). Si salva con `db.collection.insert(document)`.

### Documenti e collezioni
Un **documento** è il record: ha una primary key `_id` (creata automaticamente), salvato come **JSON/BSON/XML**, può contenere stringhe, numeri, date, **array**, oggetti e **documenti annidati**. Una **collezione** raggruppa documenti dai campi (di solito) simili.

**Paradigma schema-free:** lo schema può differire da documento a documento. Non esistono attributi "vuoti": se un campo non c'è, non era rilevante. Si può aggiungere `Field3` a *un solo* documento senza toccare gli altri né definirlo a monte.

## Embedding vs Referencing — la domanda chiave

> [!quote]
> "How often do I read these data together?"

In MongoDB la normalizzazione **non è un dogma**: i dati correlati si memorizzano in due modi.

### Embedding (annidamento)
Annida i dati nel documento padre (un documento contiene tutto). Conviene quando:
- relazione *contains*, 1:1 o 1:pochi (es. righe d'ordine dentro l'ordine);
- il dato annidato si legge sempre col padre e si aggiorna di rado da solo;
- vuoi **atomicità**: in MongoDB la scrittura su un singolo documento è atomica.

### Referencing (riferimento esterno)
Tieni i documenti separati e li colleghi via ID (come una foreign key). Conviene quando:
- relazione **many-to-many** (es. studenti e corsi);
- l'entità referenziata si aggiorna in modo indipendente o è condivisa da molti padri (es. profilo cliente);
- il lato "many" cresce **senza limiti** (log, commenti) → rischio di sbattere contro il **limite di 16 MB** per documento.

## CAP theorem

Un sistema distribuito garantisce solo **due** su tre tra Consistency, Availability e Partition tolerance — teoria completa in [[NoSQL#CAP theorem (Brewer)]].

*Analogia del professore: "Esame il giorno dopo — o lo studiamo male (consistency), o ci dai un pezzo (availability), o ne studiamo un pezzo (partition)."*

> [!important]
> **MongoDB in caso di partizione preferisce CP** — consistency + partition tolerance, sacrificando l'availability.

## Replica sets — la consistency

Gruppo di processi che mantengono gli **stessi dati** (ridondanza, maggiore disponibilità), con approccio **single-master**:
- un solo **nodo primario**, tutte le richieste passano da lui;
- se il primario cade, i restanti **votano** un nuovo master (*leader election*) — pesa la vicinanza agli altri server, la RAM, e una **priorità** assegnabile dall'utente.

## Sharding — la scalabilità (scale-out)

Architettura distribuita: ogni nodo (**shard**) gestisce una **porzione** dei dati. Una query va a ogni shard, che la elabora sulla sua porzione e restituisce il risultato; i dati sono replicati su più nodi (protezione in caso di guasto). È **scale-out**, non scale-up: aggiungi nodi commodity invece di comprare una macchina più grande — più economico, crescita virtualmente illimitata, resilienza nativa.

- Lo sharding avviene a livello di **collection**, tramite una/più **shard key**.
  `db.runCommand({shardcollection:"ecommerce.customer", key:{firstname:1}})`
- Il valore della shard key di un documento ne determina la distribuzione.
- Da Mongo 3.6 uno shard **dev'essere un replica set** (per garantire consistency).
- **mongos** è l'interfaccia client; i **config server** tengono i metadati.

## Index

Senza indice MongoDB fa una **collection scan** (legge ogni documento). Un **indice** è una struttura ordinata (**B-tree**) che memorizza una porzione dei dati in forma facile da attraversare — come l'indice analitico di un libro: salti alla pagina giusta. Il campo `_id` è indicizzato automaticamente; gli altri sono scelte di progetto. **Trade-off:** accelera le letture ma rallenta le scritture (ogni insert/update aggiorna anche l'indice) e consuma RAM/disco.

Tipi principali: **single field**, **compound** (più campi, l'ordine conta), **multikey** (automatico sugli array), **text** (full-text), **geospatial** (coordinate, "entro 1 km da…"), **TTL** (auto-cancella dopo un tempo: sessioni, log, cache), **unique** (vincolo di unicità, come `UNIQUE` in SQL).

**Rule of thumb:** indicizza i campi usati in `find()`, `sort()` e `$match`. Usa `explain()` per verificare se una query usa l'indice o fa full scan.

## Query — MongoDB vs SQL

La query usa un documento come *matcher*; `$gt` è la condizione *greater-than*. Es. "tutti i post taggati `politics` con più di 10 voti". **Attenzione:** la query SQL e quella MongoDB assumono **modelli di dati diversi** — la prima normalizzata, la seconda no.

## In pratica (PyMongo)

> [!info] Dal notebook del corso (*pyMonGo first steps*) + Atlas
> Connessione: `MongoClient()` su mongod locale, o l'URI da **Atlas** (*Connect → Drivers*).

```python
from pymongo import MongoClient
client = MongoClient("mongodb://localhost:27017/")   # o "mongodb+srv://..." di Atlas
db  = client["test-database"]      # crea il DB se non esiste
col = db["posts"]                  # crea la collezione se non esiste
db.list_collection_names()
```

Insert + query (il documento è un dict):
```python
import datetime
post = {"author": "Mike", "text": "My first post!",
        "tags": ["mongodb", "python"], "date": datetime.datetime.utcnow()}
post_id = col.insert_one(post).inserted_id          # ObjectId del nuovo documento
col.insert_many([post1, post2])

col.find_one({"author": "Mike"})                    # filtro = documento (matcher)
for d in col.find({"date": {"$lt": limite}}).sort("author", -1):   # $gt/$lt/$in/$regex
    print(d)
col.count_documents({"author": "Mike"})
```

`_id` è un **ObjectId**, non una stringa:
```python
from bson.objectid import ObjectId
col.find_one({"_id": ObjectId(str_id)})             # string → ObjectId per cercarlo
```

Aggregation pipeline (il GROUP BY di Mongo):
```python
col.aggregate([
    {"$match": {"attivo": True}},
    {"$group": {"_id": "$categoria", "tot": {"$sum": "$prezzo"}}},
    {"$sort": {"tot": -1}},
])
col.create_index("author")
```

> [!warning] Cosa NON si può / gotcha
> - Evita `$lookup` (il "JOIN" di Mongo): spesso meglio fare il join **lato Python** ([[Python|pandas]]).
> - `find()` torna un **cursor**: lo iteri **una volta** sola.
> - `_id` è un **ObjectId**: per cercarlo da una stringa serve `ObjectId(str_id)`.

> [!tip] Come fare X
> - "dedup per chiave" → pipeline `$group` (per author+text) + `$out: "collezione"`
> - "conta tutti" → `col.count_documents({})`
> - "top-N per gruppo" → `aggregate([$match, $group, $sort, {$limit: N}])`

## Vedi anche

[[NoSQL]] · [[Aggregate Oriented Model]] · [[Neo4j]]
