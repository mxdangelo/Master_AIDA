---
date: 2026-05-15
tags: [database, nosql]
status: active
area: nosql
---

# Graph databases

## Definizione formale

Un grafo è una coppia **G = (V, E)** dove:
- **V** è l'insieme dei **nodi** (vertices) — `|V| = n`
- **E ⊆ V × V** è l'insieme degli **archi** (edges) — `|E| = m`

| | **Non orientato** (undirected) | **Orientato** (directed) |
|---|---|---|
| Archi | coppie *non* ordinate: `(vi, vj) = (vj, vi)`; simmetrici | coppie ordinate: `(vi, vj) ≠ (vj, vi)`; hanno una **direzione** |
| Esempi | amicizia, co-autorship | follows, links_to, retweet |

La maggior parte dei graph DB è **orientata di default** — la direzione aggiunge *semantica* alla relazione. Un grafo non orientato si ottiene **raddoppiando ogni arco** (tranne i loop). Su un grafo orientato i nodi hanno **grado entrante** (`in-degree`) e **uscente** (`out-degree`) distinti.

## Principio fondamentale

In un grafo **l'unica cosa che conta è la relazione**. La query stessa è un **sottografo**: si cerca un *pattern* di nodi e relazioni dentro il grafo più grande (→ [[Neo4j|graph traversal]]). Il vantaggio cardine: i join si fanno per **attraversamento di percorso**, non per prodotto cartesiano.

## Tipi di struttura

- **Grafo sparso** — pochi archi rispetto ai nodi.
- **Grafo denso** — molti archi.
- **Grafo completo** — ogni nodo connesso a tutti gli altri.
- **Aciclico (DAG)** vs **ciclico** — un grafo orientato senza cicli (DAG) ammette un ordinamento dei nodi e algoritmi efficienti (scheduling, gestione di dipendenze); con un **ciclo** un percorso può ripetersi all'infinito, e problemi come il cammino *più lungo* diventano mal posti.

## Perché un graph database

- Storage efficiente e **schema-less** di dati semi-strutturati.
- Query come **graph traversal** — vincente quando ci sarebbero **tanti JOIN**.
- Spesso modellare a grafo è la scelta **naturale**: social network, routing, skill/occupazioni.
- Le proprietà **ACID** restano supportate ([[Neo4j]]).

## I modelli a confronto

| Modello | Forza | Debolezza | Uso tipico |
|---|---|---|---|
| **Relazionale** | ACID, maturo, SQL | i JOIN esplodono su legami profondi | OLTP, reporting |
| **Documentale** | schema flessibile, JSON | relazioni cross-documento scomode | cataloghi, contenuti |
| **Key/Value** | veloce, semplice | nessuna struttura né query | caching, sessioni |
| **Column** | wide, distribuito | scarso sulle relazioni | time series, IoT |
| **Grafo** | relazioni native, traversal | tooling di nicchia | reti, frodi, knowledge graph |

## Usa un grafo quando…

- il dominio è **altamente connesso** (molte relazioni);
- i dati sono **dinamici** (le relazioni cambiano spesso nel tempo);
- hai **tante operazioni di join**;
- lo **schema** può cambiare o è ignoto;
- puoi correre il rischio di una tecnologia nuova.

## Riferimenti teorici

- La **teoria dei grafi** nasce con Euler (1736, i sette ponti di Königsberg): una delle aree più longeve della matematica discreta.
- *Graph Theory* — Frank Harary (la bibbia dei grafi).
- **Algoritmo di Dijkstra** (1959) — cammino minimo: mappe, routing.

## Vedi anche

[[Neo4j]] · [[NoSQL]]
