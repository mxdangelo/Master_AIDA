---
date: 2026-09-26
tags: [statistica, data-mining]
status: active
image: "[[assets/covers/statistica.svg]]"
area: statistica
---

# Il metodo

**In una riga:** ogni scelta di un'analisi porta con sé un'ipotesi. Il lavoro è **dichiararla** e sapere se regge.

Le altre note spiegano **cosa fa** ogni tecnica e **come si calcola**. Questa spiega come si **sceglie**, e come si **difende** la scelta.

Saper calcolare una soglia serve. Saper dire **perché quella soglia** serve di più.

---

## Ogni scelta porta un'ipotesi

Le scelte sono di due tipi.

- **scelte attive**: fai qualcosa. Ribilanci il dataset, sposti la soglia, fissi `k` nella cross-validation, togli una variabile, scegli la misura
- **omissioni**: non fai qualcosa. Anche questa è una scelta, solo che non si vede

> [!example] L'omissione che non si vede: la matrice di profitto
> C'è una domanda di business vera: *"a quali clienti mando l'offerta?"*. Tu non calcoli nessuna matrice di profitto e giudichi il modello con l'accuratezza.
>
> Non hai evitato di scegliere. Hai usato la **matrice di default**: 1 sui giusti, 0 sugli errori (→ [[Valutare un classificatore#La matrice di default]]). Hai detto, senza dirlo, che **un falso positivo costa quanto un falso negativo**.
>
> A volte è legittimo. Per esempio se i costi **non si conoscono** e nessuno sa stimarli. Ma allora va **dichiarato**: *"assumo costi uguali perché non ho dati sui costi"*.

| la scelta | l'ipotesi che si porta dietro |
|---|---|
| soglia **0,5** | classi bilanciate, errori che costano uguale |
| nessuna matrice di profitto | ogni errore costa uguale |
| **accuratezza** come misura | le classi hanno peso simile: con l'1% di eventi è inutile |
| divisione **a caso** dei dati | nessun ordine nel tempo: il futuro somiglia al passato mescolato |
| **undersampling** | le probabilità vanno poi corrette col prior vero |
| togliere una variabile | l'informazione che portava c'è già nelle altre |
| riempire i mancanti con la media | mancano **a caso**, non perché il valore è scomodo |

---

## Business o statistica

Due domande diverse, due padroni diversi.

| chi decide | che cosa | esempio |
|---|---|---|
| **il business** | **cosa ottimizzare**: l'obiettivo, quanto costa ogni errore | *"perdere una frode costa 500 €, un falso allarme 5 €"* |
| **la statistica** | **come arrivarci in modo affidabile**: tecnica, validazione | *"cross-validation a 10 fold, soglia scelta sul validation"* |

La maggior parte delle scelte ha **tutti e due i lati**. La soglia, per esempio: il suo valore lo dettano i costi (business), il posto dove la cerchi lo detta la validazione (statistica).

> [!important] Come si giustifica una scelta, in tre frasi
> 1. **quale problema** risolve
> 2. **di che tipo** è quel problema: di business o statistico
> 3. **cosa ti costa**: quale ipotesi accetti, cosa perdi
>
> Se non sai dire la terza, non hai ancora capito la scelta.

---

## Le cinque domande

Sono le domande che tornano in ogni progetto. Ciascuna ha una nota che risponde.

| la domanda | se la salti | dove si risponde |
|---|---|---|
| **1. Qual è la domanda di business, e cosa ottimizzo?** | ottimizzi un numero che non interessa a nessuno | [[Valutare un classificatore]] · [[Confrontare i modelli#4. Il profitto calcolato]] |
| **2. Quanto costa un errore, e in quale verso?** | tratti uguale il falso allarme e il mancato | [[Valutare un classificatore#Criterio 2: contare i soldi]] · [[Scegliere la soglia#Scegliere la soglia sul serio]] |
| **3. I dati rappresentano i casi futuri?** | il modello impara un mondo che non esiste | [[Validazione#Come si divide]] · [[Valutare un classificatore#Gli eventi rari]] · [[Preprocessing#Prima di tutto: perché manca?]] |
| **4. Come so che il modello generalizza?** | premi un modello che ha imparato a memoria | [[Validazione]] · [[Confrontare i modelli]] · [[Alberi decisionali#La potatura]] |
| **5. So spiegare la decisione?** | nessuno si fida, e non trovi gli errori | [[Spiegare i modelli]] · [[Alberi decisionali#Pregi e difetti]] |

---

## Un esempio completo: l'undersampling

### Cos'è

Con un **evento raro** si riduce la classe maggioritaria **nel solo training**. Si tengono tutte le frodi e solo una parte delle transazioni pulite.

> [!info] Undersampling e oversampling: stesso effetto, lato diverso
> Tutte e due portano a un dataset **bilanciato**, per esempio 50/50, che **non rappresenta più la popolazione**. Quindi in tutti e due i casi le probabilità vanno corrette con i **prior veri**.
>
> Cambia **su quale classe agisci**. Esempio: 100 frodi e 9.900 transazioni regolari.
>
> | | cosa fai | il dataset | il rischio |
> |---|---|---|---|
> | **undersampling** | togli una parte dei **comuni**: 100 frodi + 100 regolari | si **riduce** (200 righe) | **butti informazione** sui comuni |
> | **oversampling** | **ripeti** i rari: 9.900 "frodi", ma solo 100 diverse | **cresce** (19.800 righe) | il modello impara **a memoria** quelle 100 frodi (overfitting) |
>
> Con l'oversampling, duplica **dentro ogni fold** della cross-validation. Se duplichi prima, una copia della stessa frode finisce nel training e un'altra nel test, e la stima esce gonfiata.
>
> *Nota sul nome, da verificare:* in [[SAS]] *oversampling* può indicare la **proporzione** (l'evento raro è sovra-rappresentato), anche quando la si ottiene togliendo i comuni. Vedi anche [[Valutare un classificatore#Gli eventi rari]].

### Le ragioni statistiche per farlo

- **troppo pochi positivi per imparare**. Un albero non trova split che isolino 30 frodi su 3.000 righe. In un [[kNN]] i vicini sono quasi tutti della classe comune, e il voto è deciso prima di cominciare
- **un dataset enorme**. Un milione di transazioni pulite aggiunge poco rispetto a centomila, e costa molto più tempo di calcolo

### Cosa ti costa

- **butti via informazione**: le righe tolte non tornano
- **le probabilità sono distorte**. Riflettono il prior del campione, non quello vero. Vanno **corrette** (→ [[Scegliere la soglia#Come si corregge]])
- di conseguenza, **0,5 sui dati ribilanciati non è 0,5 sui dati veri**. Una soglia trovata sul campione ribilanciato non vale sulla popolazione
- si fa **solo sul training, dentro ogni fold** della cross-validation. Validation e test tengono la **distribuzione vera**. Altrimenti misuri il modello su un mondo in cui le frodi sono il 30%

> [!warning] La trappola
> La lamentela tipica: *"il modello classifica tutti come negativi"*.
>
> Spesso non è un problema del **modello**. È un problema di **decisione**: con l'1% di frodi, quasi nessuna probabilità supera 0,5. Il modello può ordinare benissimo i casi, e la soglia li butta tutti dalla stessa parte.
>
> Si risolve con i **costi** e la **soglia** (business), non con l'undersampling (→ [[Scegliere la soglia#Il problema degli eventi rari]]).

### Come si decide

Non si decide a intuito. Si **confronta**:

1. modello **con** undersampling e modello **senza**, in cross-validation
2. entrambi valutati su fold con la **distribuzione vera**
3. con la **misura di business**: il profitto, se hai una matrice. Altrimenti l'**AUC**, che misura l'ordinamento e non dipende dalla soglia

Vince quello che fa meglio. Se pareggiano, tieni quello **senza**: è più semplice e non richiede correzioni.

```r
# caret ribilancia dentro ogni fold; la fetta di verifica resta com'e'
ctrl_down <- trainControl(method = "cv", number = 10, classProbs = TRUE,
                          summaryFunction = twoClassSummary, sampling = "down")
```

> [!question] All'esame, detto ad alta voce
> *"Ho fatto undersampling perché avevo 40 frodi e l'albero non trovava split. L'ho fatto solo sul training, dentro ogni fold. Ho corretto le probabilità col prior vero dell'1%. In cross-validation l'AUC passa da 0,71 a 0,78, quindi lo tengo."*

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| Non fare una cosa è una scelta? | **sì**. Anche l'omissione porta un'ipotesi |
| Nessuna matrice di profitto? | stai assumendo **costi uguali**. Dichiaralo |
| Chi decide cosa ottimizzare? | il **business** |
| Chi decide come arrivarci? | la **statistica** |
| Come giustifico una scelta? | **quale problema** risolve, **di che tipo**, **cosa costa** |
| Le cinque domande? | obiettivo · costo degli errori · dati rappresentativi · generalizza? · si spiega? |
| Undersampling, perché? | pochi positivi per imparare, o dataset enorme |
| Undersampling, cosa costa? | informazione persa, **probabilità da correggere** |
| Dove si fa? | **solo sul training**, dentro ogni fold |
| "Classifica tutti negativi"? | spesso è un problema di **soglia e costi**, non di modello |
| Come decido se ribilanciare? | con e senza, in CV, su dati veri, con **profitto o AUC** |

## Vedi anche

[[Percorso di studio]] · [[Scegliere la soglia]] · [[Valutare un classificatore]] · [[Validazione]] · [[Preprocessing]] · [[Confrontare i modelli]] · [[Spiegare i modelli]] · [[Prontuario]]
