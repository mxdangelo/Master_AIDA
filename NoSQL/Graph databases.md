---
date: 2026-05-15
tags: [database, nosql]
status: active
area: nosql
---

# Graph databases

## Definizione formale

L'idea prima della formula: un grafo è **un insieme di punti collegati da linee**. I punti sono i **nodi** (persone, luoghi, prodotti…), le linee sono gli **archi** (le relazioni tra loro: "conosce", "ha comprato", "porta a").

In simboli, un grafo è una coppia **G = (V, E)** dove:
- **V** è l'insieme dei **nodi** (*vertices*); il loro numero si indica con `|V| = n`;
- **E ⊆ V × V** è l'insieme degli **archi** (*edges*), cioè coppie di nodi; il loro numero è `|E| = m`.

| | **Non orientato** (undirected) | **Orientato** (directed) |
|---|---|---|
| Archi | coppie *non* ordinate: `(vi, vj) = (vj, vi)`; simmetrici | coppie ordinate: `(vi, vj) ≠ (vj, vi)`; hanno una **direzione** |
| Esempi | amicizia, co-autorship | follows, links_to, retweet |

La maggior parte dei graph DB è **orientata di default** — la direzione aggiunge *semantica* alla relazione. Un grafo non orientato si ottiene **raddoppiando ogni arco** (tranne i loop). Su un grafo orientato i nodi hanno **grado entrante** (`in-degree`) e **uscente** (`out-degree`) distinti.

## Principio fondamentale

In un grafo **l'unica cosa che conta è la relazione**. La query stessa è un **sottografo**: si cerca un *pattern* di nodi e relazioni dentro il grafo più grande (→ [[Neo4j|graph traversal]]). Il vantaggio cardine: i join si fanno per **attraversamento di percorso**, non per prodotto cartesiano.

## Tipi di struttura

- **Grafo sparso** — ha **pochi archi rispetto ai nodi**. Quasi tutti i grafi reali sono così: un social network ha miliardi di utenti, ma ognuno ne segue qualche centinaio.
- **Grafo denso** — ha **molti archi**: buona parte delle coppie di nodi è collegata. Es.: la rete dei voli tra i grandi hub aeroportuali, dove quasi ogni hub collega ogni altro.
- **Grafo completo** — il caso limite del denso: **ogni** nodo è connesso a tutti gli altri.
- **Aciclico (DAG)** vs **ciclico** — un **DAG** (*directed acyclic graph*) è un grafo orientato in cui nessun percorso torna al punto di partenza. Questo permette di mettere i nodi "in fila" (prima A, poi B, poi C) e abilita algoritmi efficienti: scheduling, gestione di dipendenze. Se invece c'è un **ciclo**, un percorso può ripetersi all'infinito, e problemi come il cammino *più lungo* diventano mal posti.

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

- il dominio è **altamente connesso**: le relazioni sono tante e sono il cuore della domanda ("chi conosce chi?", "cosa porta a cosa?");
- le relazioni sono **dinamiche**: nascono e muoiono di continuo, e nel grafo le aggiungi o togli senza ristrutturare nulla;
- in un relazionale servirebbero **catene di JOIN** (amici degli amici degli amici…): il traversal le sostituisce a costo costante per passo;
- lo **schema** può cambiare o non lo conosci in anticipo: il grafo è schema-less;
- il team può **permettersi il rischio di una tecnologia meno matura**: tooling, competenze e supporto sono più di nicchia rispetto al mondo relazionale (la "debolezza" nella tabella sopra).

## Riferimenti teorici

- La **teoria dei grafi** nasce con Euler (1736, i sette ponti di Königsberg): una delle aree più longeve della matematica discreta.
- *Graph Theory* — Frank Harary (la bibbia dei grafi).
- **Algoritmo di Dijkstra** (1959) — cammino minimo: mappe, routing.

## Vedi anche

[[Neo4j]] · [[NoSQL]]
