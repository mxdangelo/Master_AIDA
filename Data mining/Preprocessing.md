---
date: 2026-09-19
tags: [data-mining]
status: active
image: "[[assets/covers/data-mining.svg]]"
area: data-mining
---

# Preprocessing

**In una riga:** sistemare i dati **prima** di dargli in pasto un modello.

> [!example] È tagliare le verdure prima di cucinare
> Nessuno butta le carote intere nella pentola. Le lavi, le sbucci, le tagli della stessa misura — e solo dopo accendi il fuoco.
>
> Il preprocessing è quello. **Occupa la maggior parte del tempo di un progetto vero**, e un modello brillante su dati non preparati perde contro un modello banale su dati puliti.

I quattro controlli, sempre in quest'ordine:

```mermaid
flowchart LR
    A["1. dati<br/>MANCANTI"] --> B["2. variabili<br/>COLLINEARI"] --> C["3. varianza<br/>ZERO"] --> D["4. SCALING"]
```

---

## 1. Dati mancanti

La casella è vuota. Succede sempre: qualcuno non ha risposto, il sensore si è rotto, il campo non era obbligatorio.

> [!warning] Il modello non decide per te, si arrabbia e basta
> Quasi tutti gli algoritmi **buttano via l'intera riga** se manca un solo valore. Con dieci variabili ognuna incompleta al 5%, rischi di perdere metà del dataset senza accorgertene.

### Prima di tutto: perché manca?

Questa domanda viene prima di ogni tecnica, perché cambia la risposta giusta.

| il valore manca perché… | esempio | cosa fare |
|---|---|---|
| **non esiste** | reddito da lavoro di un bambino | è un'informazione vera: fanne una categoria "non applicabile" |
| **non è stato raccolto** | campo saltato nel questionario | si può stimare |
| **è stato omesso apposta** | nessuno dichiara uno stipendio bassissimo | **pericoloso**: l'assenza è correlata al valore, e stimarlo introduce distorsione |
| **errore tecnico** | sensore guasto | si può stimare |

> [!important] Il terzo caso è quello che frega
> Se chi guadagna poco tende a non rispondere, i valori mancanti **non sono a caso**. Riempirli con la media fa sembrare tutti più ricchi di quanto siano, e il modello eredita la bugia.
>
> Il fatto stesso che un dato manchi può essere una variabile utile: aggiungere una colonna `0/1` "questo campo era vuoto" a volte è più informativo del valore che ci metteresti dentro.

### Le strade

| strategia | cosa fa | quando |
|---|---|---|
| **buttare la riga** | elimini l'osservazione | pochi casi, e mancano a caso |
| **buttare la colonna** | elimini la variabile | manca nella maggior parte delle righe |
| **valore speciale** | metti "sconosciuto" come categoria a sé | qualitative, e l'assenza significa qualcosa |
| **imputazione semplice** | riempi con media o mediana | veloce, ma **riduce artificialmente la variabilità** |
| **imputazione da modello** | prevedi il mancante dalle altre variabili | la più accurata |

> [!info] L'imputazione con il bagging
> È quella usata nel lab, e il nome fa più paura del metodo.
>
> **Bagging** sta per *bootstrap aggregating*:
> 1. si creano **tante copie** del dataset, ciascuna estratta a sorte dall'originale
> 2. su ognuna si costruisce un [[Alberi decisionali|albero]] che prevede la variabile incompleta a partire dalle altre
> 3. il valore mancante diventa la **media delle previsioni** di tutti quegli alberi
>
> Tanti pareri invece di uno solo, e si fa la media. Stessa idea del [[Alberi decisionali#Random forest|random forest]].

---

## 2. Variabili collineari

**Due variabili che dicono la stessa cosa.** Peso in chili e peso in libbre. Metri quadri e numero di stanze. Fatturato e numero di dipendenti.

> [!warning] Perché è un problema
> Il modello deve decidere **a chi dare il merito**, e non ha modo di saperlo. Divide il credito a caso: i coefficienti diventano instabili — grandi, con segni imprevedibili, e che cambiano del tutto se togli tre osservazioni.
>
> Il sintomo classico: **il modello nel complesso funziona benissimo, ma quasi nessuna variabile risulta significativa presa da sola.** Se lo vedi, sospetta la collinearità.

### Come si misura

| indicatore | soglia di allarme |
|---|---|
| **correlazione** fra due variabili | sopra 0,8 in valore assoluto |
| **VIF** (*variance inflation factor*) | sopra **5** sospetto, sopra 10 conclamato |
| **tolleranza** (è `1/VIF`) | sotto **0,3** |

Il VIF è più furbo della correlazione: coglie anche il caso in cui una variabile è ricostruibile da **una combinazione** di più altre, cosa che il confronto a coppie non vede.

> [!tip] Come si cura
> **Togline una.** Quale? Quella meno interpretabile, o quella con più valori mancanti.
>
> Un'alternativa è comprimerle tutte in poche variabili nuove non correlate fra loro → [[PCA]].

> [!warning] Non buttarne tante insieme
> Eliminare in blocco cinque variabili correlate è pericoloso: magari tre di quelle erano le uniche a portare un'informazione che le altre non hanno. Si tolgono **una alla volta**, ricontrollando il VIF ogni volta.

> [!question] La scelta da difendere
> **Cosa scegli:** togliere una variabile. Per collinearità, per varianza quasi zero, o perché ha troppi mancanti.
>
> **Che problema risolve:** **statistico**: coefficienti instabili, calcoli che si bloccano. Ma **quale** togliere ha anche un lato di business: tieni quella che chi usa il modello capisce e sa misurare.
>
> **Cosa ti costa:** assumi che l'informazione della variabile tolta **ci sia già** nelle altre. Se non è così, la perdi.
>
> **All'esame:** *"Fatturato e numero di dipendenti hanno un VIF di 12: dicono la stessa cosa. Tolgo i dipendenti e tengo il fatturato, perché ha meno mancanti ed è il numero che l'azienda guarda. Poi ricontrollo il VIF."* → [[Il metodo]]

---

## 3. Varianza zero

Una variabile che **vale sempre lo stesso**.

Se tutti i clienti hanno `nazione = Italia`, quella colonna non distingue niente: è una domanda la cui risposta è sempre uguale. Occupa spazio e non aiuta.

C'è anche il caso quasi-zero: **999 righe su 1.000 uguali**. Tecnicamente varia, in pratica no — e in più, se finisce dentro una fetta di cross-validation, può capitare che lì dentro sia davvero costante e mandi l'algoritmo in errore.

Si tolgono entrambe.

> [!tip] Prima di buttarla, prova a ricodificarla
> Nel dataset **adult** la variabile `capgain` (guadagni in conto capitale) vale **0 per quasi tutti**. Come numero è quasi costante.
>
> Ma avere o non avere guadagni di capitale può contare per il reddito. Si trasforma in una variabile **0/1**: "ha guadagni sì/no". L'informazione utile resta, la colonna quasi costante sparisce.

### Troppe categorie

Il problema speculare: una variabile categoriale con **tante modalità, alcune rarissime**. `education` in adult ne ha 16, dal "Preschool" al "Doctorate". Una modalità con 50 persone su 32.000 dà stime instabili, e ogni modalità diventa una [[#Le variabili categoriali|dummy]] in più.

Si **raggruppano** le modalità simili: dalla prima elementare alla dodicesima classe diventano tutte `Dropout`, "ha lasciato la scuola". Lo stesso con i paesi di nascita, raccolti per area geografica.

Il raggruppamento lo decide chi conosce il fenomeno. Non c'è una regola automatica.

---

## 4. Scaling

**Rimettere tutte le variabili sulla stessa scala.**

> [!example] Perché serve
> Due variabili: età (da 18 a 80) e reddito (da 10.000 a 90.000).
>
> Un algoritmo che misura **distanze** fra persone vede una differenza di 40.000 euro e una di 30 anni. Il reddito domina tutto, non perché conti di più, ma perché è scritto in numeri più grandi.
>
> È come confrontare altezze in millimetri e pesi in tonnellate: vince sempre chi ha i numeri grossi.

### I due modi

| metodo | cosa fa | risultato |
|---|---|---|
| **standardizzazione** (*z-score*) | `(valore − media) / deviazione standard` | media 0, deviazione 1 |
| **normalizzazione** (*min-max*) | `(valore − min) / (max − min)` | tutto fra 0 e 1 |

La standardizzazione è il default: è la stessa trasformazione della [[Probabilità e distribuzioni#La standardizzazione|standardizzazione]], e regge meglio gli outlier. La min-max si usa quando serve proprio l'intervallo 0–1.

### A chi serve e a chi no

| serve | non serve |
|---|---|
| **kNN** e tutto ciò che usa distanze | [[Alberi decisionali\|alberi]] e random forest |
| **[[Clustering\|k-means]]** | [[Classificatore di Bayes\|Naive Bayes]] |
| **[[PCA]]** | |
| **reti neurali** | |
| **[[Regolarizzazione\|lasso e ridge]]** | |

> [!info] Perché agli alberi non serve
> Un albero chiede *"il reddito è sopra 30.000?"*. Che il reddito sia scritto in euro, in migliaia di euro o standardizzato, **l'ordine delle persone non cambia** — e all'albero interessa solo l'ordine. La soglia si sposta, la spaccatura è identica.

---

## Ogni modello il suo preprocessing

I quattro controlli valgono sempre. Ma ogni modello ha i suoi punti deboli, e quindi le sue priorità.

| modello | cosa gli serve | cosa gli è indifferente |
|---|---|---|
| [[Regressione logistica]] | mancanti · collinearità · osservazioni influenti · trasformare gli input · selezionare le variabili · attenzione alla **separazione** | lo scaling (serve solo con la [[Regolarizzazione]]) |
| [[Reti neurali]] | **centrare e scalare** · togliere le variabili quasi costanti (bloccano la convergenza) · togliere le molto correlate · mancanti · selezione | |
| [[kNN]] | **centrare e scalare** · imputare i mancanti · selezionare le variabili | le variabili correlate |
| [[Classificatore di Bayes\|Naive Bayes]] | imputare i mancanti · selezione consigliata | correlazione, centratura, scaling |
| [[Alberi decisionali]] | niente di specifico | quasi tutto |

> [!info] La separazione, in breve
> Succede nella logistica quando una variabile divide **perfettamente** le due classi: tutti quelli con `x > 10` sono "sì", tutti gli altri "no". Il coefficiente cresce senza fermarsi e la stima non converge (→ [[Regressione logistica#Cose che vanno storte]]).

---

## La regola d'oro

> [!warning] Il preprocessing si impara sul training, si applica al test
> È l'errore più subdolo di tutti, perché il codice gira lo stesso e i risultati sembrano ottimi.
>
> **Sbagliato:** standardizzi tutto il dataset, poi lo dividi in train e test.
> **Giusto:** dividi prima. Calcoli media e deviazione **sul solo training**. Usi *quei* numeri per trasformare il test.
>
> Perché: nel primo caso la media usata sul test contiene già informazione presa dal test. Il modello ha **sbirciato**, e il voto finale è gonfiato. Si chiama **data leakage**, fuga di informazione.
>
> Vale per tutto: imputazione, scaling, selezione delle variabili. Tutto si impara sul training. → [[Validazione]]

---

## In R

Il pacchetto è `caret`, e la funzione che fa tutto è `preProcess`.

```r
library(caret)

# 1. si IMPARA il preprocessing, sul solo training
ricetta <- preProcess(train,
                      method = c("bagImpute",   # mancanti, con gli alberi
                                 "center",      # togli la media
                                 "scale",       # dividi per la dev. standard
                                 "zv",          # varianza zero
                                 "corr"))       # collineari

# 2. si APPLICA, a entrambi
train_ok <- predict(ricetta, train)
test_ok  <- predict(ricetta, test)     # <- la STESSA ricetta, non una nuova
```

> [!important] Due passaggi separati, ed è il punto
> `preProcess()` **impara** i parametri: quali medie, quali deviazioni, quali colonne buttare.
> `predict()` li **applica**.
>
> Imparare una volta sola e applicare due volte è esattamente ciò che impedisce il leakage.

Controlli utili prima di partire:

```r
colSums(is.na(dati))              # quanti mancanti per colonna
nearZeroVar(dati, names = TRUE)   # le variabili quasi costanti
findCorrelation(cor(numeriche), cutoff = 0.8, names = TRUE)   # le collineari

library(car)
vif(glm(target ~ ., data = dati, family = "binomial"))        # il VIF
```

> [!tip] `caret` ignora i factor
> Le funzioni di preprocessing lavorano **solo sulle variabili numeriche** e saltano le categoriali senza dirlo.
>
> Due conseguenze: le categoriali vanno trattate a parte, e soprattutto **tieni fuori la variabile target**. Se il target è un `factor` viene ignorato da solo; se è numerico devi toglierlo a mano, altrimenti lo standardizzi insieme al resto.

### Le variabili categoriali

I modelli fanno conti su numeri. Una colonna `zona` con tre valori diventa **due colonne 0/1**:

| zona | `zona_centro` | `zona_sud` |
|---|---|---|
| nord | 0 | 0 | ← la **categoria di riferimento** |
| centro | 1 | 0 |
| sud | 0 | 1 |

Si chiamano **variabili dummy**. Con `k` categorie se ne creano `k − 1`: l'ultima si riconosce dal fatto che tutte le altre sono zero, e metterla creerebbe collinearità perfetta.

La categoria esclusa è il **riferimento**, e tutti i coefficienti si leggono rispetto a lei (→ [[Modelli lineari#Regressione multipla]]).

```r
dati$zona <- relevel(dati$zona, ref = "nord")   # scegli tu il riferimento
```

### Con recipes, sul dataset adult

Il pacchetto `recipes` fa la stessa cosa di `preProcess` con una sintassi a passi: ogni `step_` è un'operazione, e si leggono dall'alto in basso.

```r
library(recipes)

ricetta <- recipe(incometgt ~ ., data = train.df) |>
  step_impute_mode(all_nominal_predictors()) |>   # categoriali: la modalita' piu' frequente
  step_impute_bag(all_numeric_predictors()) |>    # numeriche: imputazione con il bagging
  step_center(all_numeric_predictors()) |>
  step_scale(all_numeric_predictors()) |>
  step_dummy(all_nominal_predictors())            # categoriali -> dummy (k - 1 colonne)

# la ricetta si passa direttamente a caret, che la rifa' dentro ogni fetta
m <- caret::train(ricetta, data = train.df, method = "glm", trControl = ctrl)
```

> [!tip] I mancanti di adult sono scritti `" ?"`
> Nel file originale un valore mancante è un **punto di domanda preceduto da uno spazio**. Se non lo dichiari, R lo legge come una categoria vera chiamata "?".
>
> ```r
> adult <- read.csv("adult.data", header = FALSE, na.strings = " ?")
> ```

Per le variabili quasi costanti, `nearZeroVar` ha una soglia regolabile:

```r
# freqCut: rapporto massimo fra la modalita' piu' frequente e la seconda.
# Il default e' 95/5 = 19. Alzarlo a 22 salva le variabili al limite.
nzv <- nearZeroVar(adult, saveMetrics = TRUE, freqCut = 22)
adult <- adult[, !nzv$nzv]     # tiene solo le colonne non quasi costanti
```

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| I quattro controlli? | **mancanti → collinearità → varianza zero → scaling** |
| Prima domanda sui mancanti? | **perché** manca. Cambia tutto |
| Riempire con la media? | veloce, ma **schiaccia la variabilità** |
| Cos'è il bagging? | tante copie, un albero per ciascuna, si fa la media |
| Sintomo di collinearità? | modello buono, **nessuna variabile significativa** |
| Soglia VIF? | sopra **5** sospetto, sopra 10 conclamato |
| Si tolgono in blocco? | **no**, una alla volta |
| Perché lo scaling? | altrimenti **vince chi ha i numeri più grandi** |
| A chi non serve? | agli **alberi**: guardano l'ordine, non la scala |
| La regola d'oro? | si impara sul **training**, si applica al test |
| Come si chiama l'errore? | **data leakage** |
| Comando R? | `preProcess()` impara, `predict()` applica |
| Variabile quasi sempre zero? | prova a farne una **0/1** prima di buttarla |
| Troppe modalità rare? | **raggruppale** per significato |
| A chi serve lo scaling? | kNN, reti neurali, regolarizzazione. **Non** ad alberi e Naive Bayes |

## Vedi anche

[[Validazione]] · [[kNN]] · [[PCA]] · [[Modelli lineari]] · [[Alberi decisionali]] · [[Data Quality]] · [[R]]
