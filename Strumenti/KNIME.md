---
date: 2026-06-13
tags: [strumenti, data-engineering]
status: active
area: strumenti
---

# KNIME

ETL **visuale** a paradigma grafico: invece di scrivere codice, componi una pipeline trascinando e collegando **nodi**. È l'alternativa *no-code* agli script Python per i flussi [[ETL]] — lo strumento del lab.

## Il paradigma a nodi e workflow

- Un **nodo** esegue un'operazione e ha input/output.
- Si concatenano in un **workflow**: ogni nodo **consuma l'output del precedente**.
- Il flusso è visibile e ri-eseguibile: si vede a colpo d'occhio cosa fa la pipeline, senza leggere codice.

Cosa fanno i nodi: **accesso ai dati** (DB, file, S3, Kafka, REST), **preprocessing**, **blending** (join/concat), **aggregazione**, **feature creation**, fino a **data mining** e **ML**.

## L'esempio del lab

Un workflow tipico end-to-end:

```
CSV → dedup → Table to JSON → MongoDB Connector → scrittura su [[MongoDB|MongoDB Atlas]]
```

Estrae da un CSV, rimuove i duplicati, converte le righe in documenti JSON e li scrive su una collezione MongoDB in cloud — tutto senza una riga di codice.

## Quando conviene

- **Sì**: flussi ETL ripetibili e condivisibili con chi non programma; prototipazione visuale; integrazione di molte sorgenti.
- **Meno**: trasformazioni molto custom o logica complessa, dove [[Python|pandas]]/[[Spark|PySpark]] restano più flessibili e versionabili.

## Vedi anche

[[ETL]] · [[MongoDB]] · [[Data Quality]] · [[Python]]
