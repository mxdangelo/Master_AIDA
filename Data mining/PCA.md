---
date: 2026-09-19
tags: [data-mining]
status: active
image: "[[assets/covers/data-mining.svg]]"
area: data-mining
---

# PCA

**In una riga:** hai sessanta variabili, ne costruisci dieci che dicono quasi tutto, e butti il resto.

**PCA** sta per *Principal Component Analysis*, **analisi delle componenti principali**.

> [!example] L'ombra dell'oggetto
> Hai una teiera e devi fotografarla. È un oggetto a tre dimensioni, la foto ne ha due: qualcosa si perde per forza.
>
> Ma esiste **l'angolo giusto**. Da lì si riconosce tutto — manico, beccuccio, coperchio. Da sopra invece vedi un cerchio e non capisci più cos'è.
>
> La PCA cerca quell'angolo, in automatico, per dati con sessanta dimensioni invece di tre. **Cerca la proiezione che perde meno informazione possibile.**

---

## Il problema che risolve

Tante variabili sembrano una ricchezza. Sono anche tre guai:

| guaio | cosa succede |
|---|---|
| **ridondanza** | metà delle variabili dicono la stessa cosa → [[Preprocessing#2. Variabili collineari\|collinearità]] |
| **rumore** | più variabili, più occasioni di imparare cose che non esistono → overfitting |
| **illeggibilità** | con sessanta variabili non disegni niente e non capisci niente |

> [!info] Perché la ridondanza è così comune
> In un questionario, *soddisfazione del servizio*, *lo consiglieresti* e *torneresti* misurano tutte la stessa cosa. In dati aziendali, fatturato, numero di dipendenti e metri quadri crescono insieme.
>
> Sessanta variabili non contengono sessanta informazioni diverse. Ne contengono forse dieci, ripetute in modi diversi. **La PCA le tira fuori.**

---

## Come funziona

> [!important] Le tre idee, in ordine
> **1. Cerca la direzione di massima variabilità.** Disegna la nuvola dei tuoi dati e cerca la direzione lungo cui i punti sono più sparpagliati. Lo sparpagliamento è informazione: lungo una direzione dove i punti sono tutti ammucchiati, non c'è niente da distinguere.
>
> Quella direzione è la **prima componente principale**.
>
> **2. Poi la seconda, perpendicolare alla prima.** Fra tutte le direzioni **a 90 gradi** dalla prima, prende di nuovo quella con più variabilità.
>
> Perpendicolare significa **non correlata**: la seconda componente porta informazione che la prima non aveva. Nessuna ripetizione.
>
> **3. E così via.** Ogni componente successiva è perpendicolare a tutte le precedenti e raccoglie quello che resta.

Il risultato: componenti **ordinate per importanza**, e ciascuna **non correlata** con le altre.

```
componente 1  →  spiega il 34% della variabilità totale
componente 2  →  18%
componente 3  →  11%
componente 4  →   7%
…
componente 60 →   0,01%     ← si buttano
```

Tenendo le prime dieci puoi arrivare al **75%** dell'informazione originale, con un sesto delle variabili.

> [!warning] Le componenti non sono le tue variabili
> Una componente è un **impasto** di tutte le variabili di partenza, ciascuna con un peso.
>
> ```
> componente 1  =  0,4 × fatturato + 0,3 × dipendenti + 0,35 × superficie − 0,1 × età …
> ```
>
> Guardando quali variabili pesano di più a volte riesci a darle un nome — "questa è la dimensione dell'azienda" — ma **non sempre**. È il prezzo della PCA: guadagni compattezza e perdi interpretabilità.

---

## Quante componenti tenere

Tre criteri, in ordine di utilità:

| criterio | come si usa |
|---|---|
| **varianza cumulata** | tieni quante servono per arrivare al 70–90% |
| **scree plot** | disegni la varianza spiegata e cerchi il **gomito** |
| **autovalore > 1** | tieni quelle che spiegano più di una variabile singola |

> [!tip] Lo scree plot e il gomito
> *Scree* in inglese è il ghiaione, la pietraia ai piedi di una montagna — e il grafico ha quella forma: scende ripido e poi si appiattisce.
>
> ```
> varianza
>  spiegata │●
>           │ ●
>           │  ●
>           │    ●
>           │      ●___         ← il GOMITO: da qui in poi e' pietraia
>           │          ●──●──●──●──●
>           └──────────────────────────
>             1  2  3  4  5  6  7  8
> ```
>
> Il gomito è il punto dove la curva smette di scendere. Le componenti dopo aggiungono briciole: si tagliano lì.

---

## Lo scaling non è opzionale

> [!warning] Senza standardizzare, la PCA è sbagliata
> La PCA cerca la **massima variabilità**. Se il reddito è in euro (varianza enorme) e l'età in anni (varianza piccola), la prima componente sarà praticamente il reddito da solo — non perché conti di più, ma perché ha i numeri più grandi.
>
> Cambia l'unità da euro a migliaia di euro e ottieni un risultato completamente diverso. Un metodo che dipende da come scrivi i numeri non serve a niente.
>
> **Si standardizza sempre prima** (→ [[Preprocessing#4. Scaling]]). In R è l'argomento `scale. = TRUE`, e va messo praticamente sempre.

---

## A cosa serve

**Comprimere le variabili prima di un modello.** Le componenti non sono correlate fra loro **per costruzione**, quindi il problema della collinearità sparisce. È la logica della *principal component regression*.

**Disegnare dati che non si potrebbero disegnare.** Con sessanta variabili non fai grafici. Con le prime due componenti sì, e spesso i gruppi si vedono già lì.

**Ridurre il rumore.** Le ultime componenti raccolgono per lo più fluttuazioni casuali. Buttandole si butta anche un po' di rumore.

> [!info] Non è la stessa cosa dell'[[Analisi discriminante]]
> Somigliano — entrambe cercano direzioni buone nello spazio delle variabili — ma cercano **cose diverse**:
>
> - **PCA** cerca le direzioni con più **variabilità**. Non sa nemmeno che esiste un target: è **non supervisionata**
> - **LDA** cerca le direzioni che **separano meglio le classi**. Il target lo usa eccome: è **supervisionata**
>
> La direzione più variabile non è necessariamente quella che distingue i gruppi.

---

## In R

```r
# 1. SOLO variabili numeriche, e scale. = TRUE
pca <- prcomp(dati[, numeriche], scale. = TRUE)

summary(pca)      # varianza spiegata, singola e cumulata
plot(pca, type = "l")   # lo scree plot: cerca il gomito

pca$rotation[, 1:3]   # i pesi: quali variabili formano le prime 3 componenti
pca$x[, 1:10]         # i PUNTEGGI: i dati riscritti nelle nuove coordinate

# le prime due componenti, disegnate
plot(pca$x[, 1], pca$x[, 2], xlab = "PC1", ylab = "PC2")
```

Dentro una pipeline `caret`, si fa in una riga:

```r
m <- train(target ~ ., data = train, method = "glm",
           preProcess = c("center", "scale", "pca"),
           trControl = ctrl)
```

> [!warning] Anche la PCA si impara sul training
> I pesi delle componenti si calcolano **sul solo training** e si applicano al test. Ricalcolarli sul test è [[Preprocessing#La regola d'oro|data leakage]].
>
> Usando `preProcess` dentro `train`, `caret` se ne occupa da solo, anche dentro ogni fetta di cross-validation. È il motivo principale per cui conviene usarlo invece di fare i passaggi a mano.

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| Cosa fa la PCA? | comprime **tante variabili in poche**, perdendo poco |
| Cosa cerca? | le direzioni di **massima variabilità** |
| Perché perpendicolari? | perpendicolare = **non correlata** = niente ripetizioni |
| Sono ordinate? | **sì**, dalla più alla meno informativa |
| Le componenti sono le mie variabili? | **no**, sono **impasti**. Si perde interpretabilità |
| Quante ne tengo? | fino al **70–90%**, o al **gomito** dello scree plot |
| Serve standardizzare? | **sempre**. Senza, vince chi ha i numeri grandi |
| Supervisionata? | **no**. Non guarda il target |
| Differenza da LDA? | PCA cerca **variabilità**, LDA cerca **separazione** |
| Comando R? | `prcomp(dati, scale. = TRUE)` |

## Vedi anche

[[Preprocessing]] · [[Regolarizzazione]] · [[Analisi discriminante]] · [[Clustering]] · [[Machine Learning]] · [[R]]
