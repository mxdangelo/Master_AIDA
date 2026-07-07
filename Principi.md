---
tags: [meta]
---

# Principi

I principi che tornano in tutti gli appunti — definiti **qui una volta**, citabili con un link `[[Principi]]` o **transclusi** con `![[Principi#^id]]` (l'id è il `^...` in fondo a ogni riga).

**Garbage in, garbage out** — un'analisi vale quanto i dati che la nutrono. ^gigo

**All models are wrong, but some are useful** (George Box) — un modello è una semplificazione *utile* della realtà, non la realtà. ^modelli

Il **dato giusto all'attante giusto**, nella forma giusta per l'interlocutore — i KPI cambiano col destinatario. ^attante

**Move computation to data** — sposta il codice (piccolo) verso i dati (enormi), non il contrario. È il principio di [[Hadoop]]. ^move-compute

Un buon processo dati è **idempotente** — rilanciarlo non duplica né corrompe (upsert, non append ciechi). → [[ETL#Idempotenza|ETL]]. ^idempotenza

Prima di tutto, **farsi dire bene qual è la domanda di business** — da lì prende senso tutto il resto. ^domanda

**Single responsibility** (Robert C. Martin) — ogni modulo deve avere **una sola ragione di cambiare**: tieni insieme ciò che cambia insieme, separa ciò che cambia per motivi diversi. Vale per funzioni, classi, note — e per i [[Git e GitHub#Commit atomici|commit]]. ^srp

## Vedi anche

[[Prontuario]] · [[Dashboard]]
