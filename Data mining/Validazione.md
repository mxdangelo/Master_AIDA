---
date: 2026-09-19
tags: [data-mining]
status: active
image: "[[assets/covers/data-mining.svg]]"
area: data-mining
---

# Validazione

**In una riga:** capire quanto il modello funzionerà su gente che **non ha mai visto**.

> [!example] Studiare per un esame
> Ti eserciti su dieci compiti vecchi finché li fai tutti giusti. Sei bravo, o hai imparato a memoria quei dieci?
>
> L'unico modo di saperlo è **un compito nuovo**. Se vai bene lì, hai capito la materia. Se crolli, avevi memorizzato.
>
> Un modello è identico. **Quanto sbaglia sui dati con cui è stato addestrato non dice niente**, e spesso mente.

Il rischio ha un nome: **overfitting**. Il modello impara i dettagli e il rumore del suo training, e su dati nuovi crolla (→ [[Alberi decisionali#Il problema: se lo lasci correre, impara a memoria]]).

> [!example] Il nastro dei pesci
> Un impianto vuole separare in automatico salmoni e spigole, guardando colore delle squame e lunghezza.
>
> Sui dati di addestramento una logistica fa il **95,7%** di risposte giuste. Una rete neurale fa il **99,9975%**.
>
> Il secondo numero è un campanello d'allarme. Un'accuratezza così vicina al 100% sui dati già visti è il segno tipico di un modello che ha **imparato a memoria** quei pesci. Quanto vale davvero lo si scopre solo sui pesci di domani.

Per questo la **generalizzazione** si misura sempre su dati **completamente separati** da quelli usati per costruire il modello.

> [!tip] Per scegliere un modello, due criteri, in quest'ordine
> 1. che sia **robusto** su dati nuovi
> 2. che classifichi o preveda **bene**
>
> Un modello che va benissimo sul training e crolla fuori non si tiene, qualunque sia il suo punteggio.

---

## I quattro passi del data mining

Il data mining non costruisce **un** modello. Ne costruisce tanti, e poi sceglie. I passi sono sempre quattro:

```mermaid
flowchart LR
    A["1 · COSTRUIRE<br/>tanti modelli<br/>sul training"] --> B["2 · ASSESSMENT<br/>confrontarli<br/>su dati nuovi"]
    B --> C["3 · VALUTARE<br/>il modello scelto,<br/>con la sua soglia"]
    C --> D["4 · SCORE<br/>applicarlo<br/>ai casi nuovi"]
```

| passo | cosa fai | dove approfondire |
|---|---|---|
| **1. costruire** | logistica, alberi, kNN, reti… ciascuno con il suo preprocessing | [[Preprocessing]] |
| **2. assessment** | confronti i modelli con misure **che non dipendono dalla soglia** | [[Confrontare i modelli]] |
| **3. valutare** | scegli la soglia e misuri il modello scelto sulla matrice di confusione | [[Scegliere la soglia]] · [[Valutare un classificatore]] |
| **4. score** | usi il modello per classificare casi di cui **non conosci** la risposta | [[Scegliere la soglia#Lo score dei casi nuovi]] |

> [!info] Cosa distingue il data mining dalla statistica classica
> I passi 2 e 3 sono tipici del data mining. La statistica classica stima **un** modello e lo giudica sugli stessi dati.
>
> Qui si passa al passo 4 **solo se** il modello vincente va bene su dati indipendenti.

> [!warning] Validation e test: occhio al vocabolario
> Molti testi e software usano **"validation" e "test" come sinonimi**: "il dataset messo da parte". In questa nota sono due cose diverse: la validation serve a scegliere, il test a misurare una volta sola alla fine.
>
> La distinzione conta al passo 3. Se sul validation hai scelto il modello **e** la soglia, quel validation è "contaminato" dalle scelte. Per questo si consiglia un **terzo dataset indipendente**, il test, per la misura finale.

---

## Misurare l'errore

Prima di dividere i dati, serve un numero che dica **quanto sbaglia** il modello.

| target | misura | formula |
|---|---|---|
| **numerico** (un prezzo) | **ASE**, errore quadratico medio | media di `(y − ŷ)²`: `ŷ` è il valore previsto |
| **binario** (sì/no, codificato 1/0) | **ASE** sulle probabilità | media di `(y − p)²`: `p` è la probabilità prevista |
| **binario**, dopo la soglia | **accuratezza** ed **error rate** | dalla [[Valutare un classificatore\|matrice di confusione]] |

> [!warning] L'errore sul training è troppo ottimista
> Ogni misura calcolata sui dati di training usa previsioni fatte **da un modello che quei dati li ha visti**. È distorta, sempre nello stesso verso: sembra migliore di quanto sia.
>
> Per questo servono le tecniche di validazione che seguono.

> [!tip] La regola del pollice per l'overfitting
> Confronta l'errore sul validation con quello sul training:
>
> ```
> (ASE validation − ASE training) / ASE training   <   5% – 7%
> ```
>
> Se il validation sbaglia **molto più** del training, oltre questa soglia, sospetta overfitting.
>
> Esempio: ASE training 0,150, ASE validation 0,168. Differenza relativa `0,018 / 0,150` = 12%. Troppo: il modello sta memorizzando.

---

## Dividere i dati

```mermaid
flowchart LR
    D["tutti i dati"] --> T["TRAINING<br/>~70%<br/>qui impara"]
    D --> V["VALIDATION<br/>~15%<br/>qui scegli"]
    D --> S["TEST<br/>~15%<br/>qui misuri"]
```

| insieme | a cosa serve | quante volte lo guardi |
|---|---|---|
| **training** | il modello impara qui | continuamente |
| **validation** | scegliere fra modelli e regolare le manopole | molte volte |
| **test** | la stima onesta di quanto funziona | **una volta sola, alla fine** |

> [!important] Perché servono tre insiemi e non due
> Sembra che training e test bastino. Non basta, e il motivo è sottile.
>
> Se provi venti modelli e tieni quello che va meglio **sul test**, hai usato il test per scegliere. Quel test non è più "dati mai visti": l'hai consultato venti volte, e il modello vincente è in parte vincente per fortuna su *quei* dati.
>
> Il risultato che riporti è **gonfiato**. La validation esiste proprio per fare le scelte, così il test resta vergine.

> [!warning] Il test si guarda alla fine, e una volta sola
> Se guardi il test, non ti piace il numero e cambi il modello, **hai appena bruciato il test**. Da quel momento è diventato un validation set, e non hai più una stima onesta.
>
> Questa è la regola che chi comincia rompe sempre.

### Come si divide

A caso, ma con una precauzione:

> [!tip] Dividi in modo stratificato
> Se gli insolventi sono il 5%, una divisione a caso può metterne 8% nel training e 2% nel test. I due insiemi non sono più confrontabili.
>
> La divisione **stratificata** mantiene le stesse proporzioni ovunque. In `caret` è il default di `createDataPartition`.

> [!info] Validazione esterna e interna
> Dividere in training e validation si chiama **validazione esterna** (*holdout*): il modello si verifica su un dataset nuovo. Le proporzioni tipiche sono **60–70%** training e **30–40%** validation.
>
> - **pro:** le previsioni sul validation vengono da un modello che quelle righe non le ha usate. Non sono distorte
> - **contro:** una parte dei dati non serve ad addestrare
>
> Con pochi dati — sotto le **1.000–2.000 osservazioni** — si preferisce la **validazione interna**: cross-validation o bootstrap, che riusano tutti i dati (vedi sotto).

E se i dati hanno un ordine temporale — previsioni, serie storiche — **non si divide a caso**: si addestra sul passato e si verifica sul futuro. Altrimenti il modello impara da dati che nella realtà non avrebbe ancora avuto. È un'altra forma di [[Preprocessing#La regola d'oro|leakage]].

---

## Cross-validation

Il problema di mettere da parte una fetta: **butti via dati**. Con 200 osservazioni, toglierne 60 per il test è doloroso. E il risultato dipende da quali 60 ti sono capitate.

La **cross-validation** risolve entrambe le cose.

> [!important] Come funziona, in tre righe
> 1. dividi i dati in **k fette** (di solito **k = 5** o **k = 10**)
> 2. addestri su `k−1` fette e verifichi sulla fetta rimasta
> 3. **ripeti k volte**, cambiando ogni volta quale fetta fa da verifica
>
> Alla fine hai k misure di errore, e prendi la **media**.

```mermaid
flowchart TD
    A["giro 1 — verifica sulla fetta 1, allena sulle altre"] --> B["giro 2 — verifica sulla fetta 2"]
    B --> C["giro 3, 4… fino a k"]
    C --> D["media dei k errori<br/>= la stima"]
```

Il guadagno è doppio: **ogni osservazione fa da dato nuovo esattamente una volta**, e la media di k misure balla molto meno di una misura sola.

> [!info] Quante fette?
> - **k = 5 o 10** è lo standard. Buon compromesso fra affidabilità e tempo
> - **k più grande** = stima più accurata, ma k addestramenti da fare
> - **k = n** (una fetta per osservazione) si chiama *leave-one-out*: precisissima e lentissima, si usa solo con pochissimi dati

> [!question] La scelta da difendere
> **Cosa scegli:** come stimi l'errore su dati nuovi. Holdout o cross-validation, e con quanti fold.
>
> **Che problema risolve:** **statistico**. Ti serve una stima onesta di come andrà il modello fuori dal training.
>
> **Cosa ti costa:** l'holdout toglie dati all'addestramento, e il risultato dipende da quali righe sono capitate. La cross-validation costa `k` addestramenti. Ogni divisione **a caso** assume che non ci sia un ordine nel tempo. Ogni divisione assume che le fette somiglino ai casi futuri.
>
> **All'esame:** *"Ho 800 righe, sotto le 1.000–2.000. Quindi cross-validation a 10 fold, stratificata perché gli eventi sono il 5%. Preprocessing rifatto dentro ogni giro, test messo da parte e guardato una volta sola."* → [[Il metodo]]

### Leave-one-out e PRESS

Nel *leave-one-out* si toglie **una riga alla volta**. Si addestra sulle altre `n − 1` e si prevede la riga tolta. Si ripete `n` volte, finché ogni riga è stata prevista. Il procedimento si chiama anche **jackknife**.

Con un target numerico, gli errori al quadrato di quelle `n` previsioni si sommano in un numero con un nome proprio:

```
PRESS = somma di (y − ŷ senza quella riga)²
```

**PRESS** sta per *predicted residual sum of squares*. Diviso per `n` diventa l'ASE cross-validato. Serve, fra l'altro, a scegliere la penalità della [[Regolarizzazione]].

> [!tip] Le misure non cambiano, cambia da dove vengono le previsioni
> In cross-validation si calcolano le stesse misure di sempre: ASE, accuratezza, error rate. L'unica differenza è che la previsione di ogni riga viene dal giro in cui **quella riga era esclusa**.

> [!warning] La cross-validation non sostituisce il test set
> Serve a **scegliere** — quale modello, quali manopole. Fa il lavoro della validation, non quello del test.
>
> Il test messo da parte all'inizio resta lì, intatto, per la misura finale.

E vale sempre la regola d'oro: **anche il preprocessing va rifatto dentro ogni giro**. Se standardizzi tutto prima di dividere in fette, ogni giro ha già visto un pezzo della sua fetta di verifica.

---

## Bootstrap

Un'altra idea, per un'altra domanda: **quanto è incerta la mia stima?**

> [!example] Il sacchetto delle biglie
> Hai 100 osservazioni. Ne estrai 100 **rimettendo dentro** ogni volta quella pescata.
>
> Ottieni un dataset nuovo da 100, in cui qualcuna compare due o tre volte e qualcun'altra non compare affatto. Ripeti **mille volte**: mille dataset diversi, tutti plausibili quanto il tuo.
>
> Calcoli il tuo numero su ciascuno, e guardi **quanto balla**.

Il punto: non potendo raccogliere mille campioni veri, li **simuli** dal campione che hai. È lo stesso ragionamento sulla [[Inferenza#La stima è un numero ballerino|variabilità delle stime]], fatto a forza bruta invece che con una formula.

Serve quando l'intervallo di confidenza non ha una formula comoda, oppure per stimare l'incertezza di cose strane — la differenza fra due mediane, un indice costruito a mano.

### Validare con il bootstrap

Il bootstrap serve anche a **validare un modello**. L'ingrediente chiave: in ogni estrazione con reimmissione, una parte delle righe **non viene mai pescata**. Si chiamano **out of bag** (*OOB*), "rimaste fuori dal sacchetto".

1. estrai un campione bootstrap: è il **training** di quel giro
2. le righe rimaste fuori sono il **validation** di quel giro
3. addestri, misuri accuratezza ed errore sulle righe fuori
4. ripeti **r volte** (di solito almeno 200) e fai la **media**

Oltre alla media, guardi **quanto ballano** le r misure. Se variano molto da un campione all'altro, il modello è instabile. Un modello robusto dà risultati simili ogni volta.

E il bootstrap è anche il mattone del **bagging**, che sta dentro il [[Alberi decisionali#Random forest|random forest]] e dentro l'[[Preprocessing#Le strade|imputazione dei mancanti]].

---

## Confrontare più modelli

Una volta che ogni modello ha la sua misura in cross-validation, si confrontano. Con una cautela:

> [!warning] Una differenza piccola non è una differenza
> Modello A: 0,82 di accuratezza. Modello B: 0,81. **Non hai dimostrato che A è migliore.**
>
> Ogni misura ha la sua incertezza, perché è la media di k giri che fra loro variano. Se le due fasce si sovrappiano, i modelli sono equivalenti.
>
> Guarda la **dispersione fra i fold**, non solo la media. È lo stesso principio della [[Alberi decisionali#La regola a una deviazione standard|regola a una deviazione standard]]: a parità di risultato, vince il modello **più semplice**.

---

## In R

Tutto passa da `caret`, e da due funzioni.

```r
library(caret)

# 1. dividere, in modo stratificato
set.seed(1)
i <- createDataPartition(dati$target, p = 0.7, list = FALSE)
train <- dati[i, ]
test  <- dati[-i, ]

# 2. impostare la validazione
ctrl <- trainControl(method = "cv", number = 5, classProbs = TRUE)

# 3. addestrare: caret fa da solo i 5 giri
m <- train(target ~ ., data = train, method = "glm",
           family = "binomial", trControl = ctrl)

m               # la stima cross-validata
m$resample      # i 5 risultati, uno per fetta: guarda quanto ballano
```

Confrontare più modelli sulle **stesse** fette:

```r
alberi <- train(target ~ ., data = train, method = "rpart", trControl = ctrl)
knn    <- train(target ~ ., data = train, method = "knn",   trControl = ctrl)

confronto <- resamples(list(logistica = m, albero = alberi, knn = knn))
summary(confronto)
bwplot(confronto)     # i boxplot delle metriche: si vede la sovrapposizione
```

E solo alla fine, una volta sola:

```r
previsti <- predict(m, newdata = test)
confusionMatrix(previsti, test$target)
```

> [!tip] `method` dentro `trainControl`
> `"cv"` cross-validation · `"repeatedcv"` la ripete più volte con divisioni diverse · `"boot"` bootstrap · `"none"` nessuna validazione.
>
> `set.seed()` prima di dividere serve a poter **rifare identico** lo stesso esperimento. Senza, ogni esecuzione dà numeri diversi e non capisci più se è cambiato il modello o solo il sorteggio.

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| Perché validare? | l'errore sui dati **già visti** mente |
| I tre insiemi? | **training** impara · **validation** sceglie · **test** misura |
| Quante volte guardo il test? | **una sola, alla fine** |
| Perché non bastano due insiemi? | scegliere sul test **gonfia** il risultato |
| Cos'è la cross-validation? | k fette, k giri, si fa la **media** |
| Quanti fold? | **5 o 10** |
| Sostituisce il test? | **no**. Fa il lavoro della validation |
| Cos'è il bootstrap? | riestrai **con reimmissione** per stimare l'incertezza |
| Divisione stratificata? | mantiene le **proporzioni** delle classi |
| Dati temporali? | si allena sul **passato**, mai a caso |
| A vince 0,82 contro 0,81? | **no**: guarda la dispersione fra i fold |
| Comandi R? | `createDataPartition`, `trainControl`, `train`, `resamples` |
| I quattro passi? | **costruire → assessment → valutare → score** |
| Regola del pollice? | ASE validation oltre il **5–7%** sopra il training = sospetto overfitting |
| Holdout o interna? | sotto **1.000–2.000** righe, cross-validation o bootstrap |
| **PRESS**? | la somma degli errori al quadrato del **leave-one-out** |
| **Out of bag**? | le righe non pescate dal bootstrap: fanno da validation |

## Vedi anche

[[Preprocessing]] · [[Confrontare i modelli]] · [[Valutare un classificatore]] · [[Scegliere la soglia]] · [[Alberi decisionali]] · [[Machine Learning]] · [[R]]
