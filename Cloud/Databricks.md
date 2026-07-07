---
date: 2026-06-26
tags: [cloud, big-data]
status: active
image: "[[assets/covers/cloud.svg]]"
area: cloud
---

# Databricks

Piattaforma analitica **collaborativa basata su [[Spark|Apache Spark]]** che rimuove la complessità di creare e gestire un cluster (installazioni e setting con un click). È la piattaforma di riferimento per il **lakehouse** ([[ETL#Data warehouse vs data lake|warehouse vs lake]]): una sola base governata serve BI, ML e analitica.

## Cosa include

- **Moduli Spark** già pronti: Spark SQL, Streaming, **MLlib**, GraphX.
- **Notebook** stile Jupyter: Python/Scala/SQL nello stesso notebook, sessione `spark` **già globale**, integrazione con GitHub e con AWS/Azure/GCP.
  - Trucco: usa `display(df)` invece di `df.show()` → tabella interattiva, ordinabile, con grafici.

## Lo stack lakehouse

Tutto sugli **stessi dati governati**:

| Componente | Ruolo |
|---|---|
| **Delta Lake** | storage: tabelle ACID su file aperti (Parquet), *schema enforcement*, **time travel** |
| **Spark + Photon** | motore di calcolo (Photon = engine vettorizzato, più veloce) |
| **Delta Live Tables** | pipeline **ELT** dichiarative che costruiscono i layer [[ETL#Medallion architecture|medallion]] (Bronze/Silver/Gold) |
| **Unity Catalog** | governance, permessi, *lineage* |
| **Databricks SQL** | interfaccia BI/query |
| **MLflow** | tracking e gestione del ciclo di vita dei modelli ML |

> [!info] Delta Lake da solo
> **Delta Lake** ([delta.io](https://delta.io)) è open-source e usabile anche fuori Databricks, sopra [[Spark]]: transazioni ACID, *schema enforcement*, time travel, upsert/delete, streaming + batch unificati. È ciò che rende un [[Dati#Data Lake|data lake]] un **lakehouse**.

## Dove si colloca

- Alternativa gestita a montare a mano un cluster [[Hadoop]]/[[Spark]] (come fa [[AWS#Setup pratico — EMR|EMR]] su AWS, ma più integrata e collaborativa).
- Sul cloud (AWS/Azure/GCP): è un **SaaS/PaaS** analitico → [[Cloud computing#Modelli di servizio|modelli di servizio]].

## Vedi anche

[[Spark]] · [[ETL]] · [[Cloud computing]] · [[AWS]]
