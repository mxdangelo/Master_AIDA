---
date: 2026-06-26
tags: [cloud]
status: active
image: "[[assets/covers/cloud.svg]]"
area: cloud
---

# Cloud computing

La tecnologia nasce sempre da un **bisogno di business**: il cloud è la risposta alla domanda crescente di potenza di calcolo, dati e reattività.

## Evoluzione storica

- **Anni '50 — mainframe.** Un computer, molti utenti (*1 computer, many users*); parte da American Airlines. Diventa l'ossatura di settore bancario, assicurativo e governo. La Cina, nel 2018, investe in mainframe proprietari.
- **Anni '70 — dal centralizzato ai server.** La decentralizzazione non è solo geografica ma anche **per servizi**: server dedicati al singolo servizio (→ microservices), parcellizzazione.
- **2000 — cloud computing.** Architettura distribuita, *serverless*, modello **pay-per-use**: le aziende possono sperimentare. La nascita delle startup dipende in larga parte da questo — senza cloud bisogna sempre procurarsi e distribuire la potenza di calcolo in proprio.

## Virtualizzazione

Architettura virtuale: le risorse si spostano da una macchina all'altra in base alle necessità del momento. Tecnologizzazione e interconnessione alimentano la crescita delle richieste, aumentano i dati (vedi la crescita esponenziale dei nuovi prodotti sul mercato — l'adozione di TikTok, ChatGPT): diviene necessario un nuovo paradigma.

## I tre pilastri del nuovo paradigma

### 1. Microservizi

Decomposizione **funzionale** del sistema in componenti **deployabili in modo indipendente** (Lewis & Fowler). Ognuno fa una cosa e si rilascia, scala, aggiorna da solo — senza ridistribuire il monolite.
- **Docker** — risolve il problema *"gira ovunque"* coi **container** (Linux containers): impacchettano applicazione + librerie + versioni, isolandole. Evitano il conflitto classico *"in sviluppo funziona, in produzione no"* (es. Python3 + lib v2 vs Python2 + lib v1: ogni servizio nel suo container, niente collisione).
  - **Immagine** — snapshot **immutabile**, costruito da un **Dockerfile**; sta in un *registry* (come il file `.ova` scaricato per il Master).
  - **Container** — l'esecuzione dell'immagine (la "VM effettiva").
- **Kubernetes** — orchestrazione dei container; poi la versione più digeribile fatta dalla community.

Ma non basta: servono sempre più servizi e complessità, affidabilità, scalabilità.

### 2. Reattività: da CapEx a OpEx

Il cloud sposta la spesa da capitale (CapEx) a operativa (OpEx):
- Non si comprano più i server *upfront*, non si assume personale per le macchine: si **noleggia**. Ragionamento operazionale — non serve andare in banca a chiedere soldi.
- Si modula il contratto col fornitore: potenza di calcolo per mesi, minuti, secondi, poi si spegne. **Economia di scala.**
- Si finisce per offrire non solo il ferro (il server), ma anche il database (login, installazioni…) e infine il software: **SaaS** — i bisogni delle aziende sono simili, si sviluppa una volta e si vende. L'azienda esternalizza i software non-core (CRM ecc.). Così nascono Salesforce, Google… → **servitisation**.

### 3. Cosa è il cloud computing

> [!info]
> *Web-based computing resources, from servers and storage to enterprise-level applications.*

**Le 5 caratteristiche** — cosa deve avere un servizio per dirsi *cloud*:

1. **On-demand self-service** — le risorse te le prendi da solo, quando servono: un pannello web, non una trattativa commerciale. Puoi accendere un server alle 3 di notte senza parlare con nessuno.
2. **Network access** — tutto è raggiungibile **via rete**, da qualunque dispositivo standard (browser, API): niente accesso fisico alla macchina.
3. **Resource pooling** — il fornitore mette l'hardware in un unico "serbatoio" **condiviso tra i clienti**: tu non sai (né ti serve sapere) su quale macchina fisica giri.
4. **Rapid elasticity** — la capacità si espande e si contrae **seguendo il carico**, tanto in fretta che al cliente sembra infinita. Un e-commerce decuplica i server per il Black Friday e li spegne il lunedì.
5. **Measured service** — ogni consumo è **misurato** e paghi quello che usi. Le garanzie (uptime, prestazioni) le fissa il contratto: il **SLA** (*service-level agreement*).

**Vantaggi:** risorse percepite come "infinite", scalabilità, gestione semplificata (il ferro è un problema del fornitore).

**Fattori frenanti:**
- **security e compliance** — il dato esce dal perimetro aziendale: chi lo protegge, secondo quali norme?
- **trasparenza** — non vedi come il fornitore gestisce davvero le tue risorse;
- **portabilità** — migrare da un provider all'altro costa (*lock-in*);
- **comparazione dei costi** — con cosa confronti il prezzo? Il "pari servizio" on-premise è difficile da quantificare;
- il passaggio a OpEx ha un rovescio: noleggiare invece di possedere **riduce il valore patrimoniale** dell'azienda rispetto al CapEx.

## Modelli di servizio

Quanto dello stack affitti, dal ferro all'applicazione:

| | Cosa affitti | Esempio |
|---|---|---|
| **IaaS** — Infrastructure as a Service | CPU, storage, rete, OS — l'infrastruttura nuda | EC2, S3 |
| **PaaS** — Platform as a Service | piattaforma per sviluppare e deployare app, coi linguaggi supportati dal provider | AWS Glue, Athena |
| **SaaS** — Software as a Service | l'applicazione pronta, via interfaccia/API | Salesforce, Gmail, Tableau Online |

Le grandi aziende scelgono spesso una **soluzione ibrida**, a seconda di cosa è core e cosa no.

### Modelli di deployment

Chi possiede l'infrastruttura, e chi può usarla:

- **Public** — l'infrastruttura è del provider (AWS, Azure, GCP) e la condividi con gli altri clienti. Costi ridotti, niente manutenzione, scalabilità quasi illimitata, alta affidabilità.
- **Private** — l'infrastruttura serve **una sola organizzazione** (in casa propria o ospitata): sicurezza personalizzata e controllo esclusivo, al prezzo di costi e gestione. Due varianti: *packaged* (preconfezionata) o *custom* (su misura).
- **Hybrid** — un mix dei due: il core sensibile resta nel private, i picchi di carico e il non-critico vanno sul public.
- **Community** — un private **condiviso tra più organizzazioni con gli stessi requisiti** (es. enti sanitari o PA con gli stessi vincoli normativi), che dividono costi e governance.

### Prezzo
Domande da porsi: quanto costa 1h di calcolo su un server "pronto" per big data? 1 GB trasferito? 1 GB di storage al mese? E manutenzione, recovery, aggiornamenti, licenze?
- **Spot instance** — sfrutti la capacità **inutilizzata** del cloud, sconti **fino al 90%** sull'on-demand. Prezzo del compromesso: sono **interrompibili** quando il prezzo spot supera il tuo massimo o la domanda sale. Vanno usate per carichi tolleranti alle interruzioni.
- **Reserved instance / Saving plan** — pagamento anticipato per uno sconto forte sull'uso orario di un'istanza riservata.

## AWS

I servizi concreti di AWS — mattoni (S3, EC2, EMR, IAM, VPC), mappa dei servizi, serverless, Glue/Athena e la pipeline del lab — hanno una **nota dedicata**: → [[AWS]].

## Data architecture moderna

### Data lake — il paradigma
Cos'è il data lake e le sue fonti: [[Dati#Data Lake]]. Schema-on-write vs schema-on-read e il lakehouse: [[ETL#Data warehouse vs data lake]]. Qui il taglio è la **differenza di filosofia** col warehouse:

| **Data Warehouse** | **Data Lake** |
|---|---|
| sottoinsiemi aggregati, viste on-demand | **store everything as-is** |
| curato da esperti, strutturato (tabelle/report) | il business decide cosa serve, quando |
| qualità nota e tracciata | dal grezzo al conformato; qualità non sempre nota |
| top-down | rapido cambiamento, *lineage* e storicizzazione, esplorazione bottom-up |

### Delta Lake — il lakehouse
Tecnologia open-source ([delta.io](https://delta.io)) sopra **[[Spark]]** per data lake robusti: **transazioni ACID** su Spark, *schema enforcement*, **time travel**, upsert/delete, unificazione streaming + batch. È la base del **lakehouse** ([[ETL]]).

*Le parti AWS-specifiche — Glue, Athena e la pipeline event-driven del lab (TEDx) — sono nella nota [[AWS]].*

## Implicazioni organizzative e di rete

*(Sezione di sintesi a partire dallo spunto delle slide — "organisational and networking analysis" — non una loro trascrizione.)*

### Organizzazione: chi decide cambia
Le slide contrappongono l'**IT centrale** (un'unica funzione che fa da gatekeeper a tutti i servizi delle business unit) all'approccio **distribuito** (ogni unità possiede i propri servizi). Il cloud abilita il secondo, e con esso uno spostamento di *potere decisionale*:

- **Conway's law** *(ipotesi/inquadramento)* — l'architettura di un sistema rispecchia la struttura di comunicazione di chi lo costruisce. [[#1. Microservizi|Microservizi]] ↔ **team piccoli e autonomi**: non scegli prima la tecnologia e poi l'organizzazione, le due si plasmano a vicenda.
- Il passaggio **CapEx → OpEx** toglie il *gate* dell'acquisto hardware: non serve più passare dalla funzione che compra i server. Più autonomia per i team, ma anche meno controllo centrale sulla spesa → serve **governance** (FinOps, budget per team).
- **Implicazione operativa**: il cloud significa **affittare capacità** invece di possederla. Non si gestisce ferro né cluster — si noleggia a consumo e si concentra il tempo sul *core*, esternalizzando l'*undifferentiated heavy lifting*. È lo stesso ragionamento del SaaS visto sopra.

### Rete: il dato ha gravità
- **Data gravity** *(inquadramento)* — è più economico portare il **calcolo dove sta il dato** che il contrario. È lo stesso principio di [[Hadoop|Hadoop]] ("move computation to data"), qui a scala cloud: collochi compute e storage nella stessa **Region** per non pagare latenza e transfer.
- **Costi di egress** — far *entrare* i dati nel cloud è gratis o quasi; **farli uscire** (egress) o spostarli tra region/provider è la voce che pesa. Progetta per **minimizzare i movimenti** cross-region/cross-cloud — è anche ciò che rende [[AWS#Glue + Athena|Athena su S3]] conveniente (il dato non si sposta, la query va al dato).
- **Perimetro di sicurezza** — VPC, ruoli, isolamento: ogni servizio esposto è una superficie in più. Parallelo con la "nuova superficie privacy" di un LLM che manda dati a un'API terza ([[BI Architecture#Dalla BI all'analitica AI-augmented|MCP]], [[Data Ingestion#Etica e legalità|scraping]]).
- **Identità di rete** — dietro CGNAT si condivide l'IP (e i throttle) con altri; in cloud è l'opposto e il punto di forza: **IP, region e zone si scelgono**, e con esse latenza, resilienza e conformità (dove risiede il dato).

---

## Vedi anche

[[AWS]] · [[Databricks]] · [[BI Architecture]] · [[Dati]] · [[Spark]] · [[Hadoop]] · [[ETL]] · [[Aggregate Oriented Model]] · [[Data Ingestion]]
