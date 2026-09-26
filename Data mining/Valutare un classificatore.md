---
date: 2026-09-25
tags: [data-mining]
status: active
image: "[[assets/covers/data-mining.svg]]"
area: data-mining
---

# Valutare un classificatore

**In una riga:** hai scelto il modello e la soglia. Ora misuri quanto classifica bene, in due modi: **contando le persone** che azzecca, o **contando i soldi** che fa guadagnare o perdere.

Questa nota viene **dopo** la scelta del modello. Il confronto fra più modelli è un'altra operazione, con altre misure (→ [[Confrontare i modelli]]). L'ordine completo sta in [[Validazione#I quattro passi del data mining]].

> [!important] Le due strade
> 1. **in base alle unità classificate** — quante persone il modello mette nella classe giusta. Tutto parte dalla **matrice di confusione**
> 2. **in base ai costi** — quanto vale ogni giusto e quanto costa ogni errore. Si parte dalla matrice di confusione e la si moltiplica per una **matrice di profitto**
>
> Possono dare verdetti diversi. Un modello meno accurato può far guadagnare di più, se sbaglia sugli errori che costano poco.

---

## Da dove viene la matrice di confusione

Il modello non dà classi. Dà una **probabilità** per ogni persona. Per avere una matrice di confusione servono due ingredienti:

1. le **probabilità stimate sul validation**, cioè su dati che il modello non ha usato per imparare
2. una **soglia**, per trasformare ogni probabilità in "sì" o "no" (→ [[Scegliere la soglia]])

Poi si incrocia il previsto con il vero:

| | **è davvero sì** | **è davvero no** |
|---|---|---|
| **prevedo sì** | **VP** — vero positivo | **FP** — falso positivo, falso allarme |
| **prevedo no** | **FN** — falso negativo, mancato | **VN** — vero negativo |

`VP + FP + FN + VN = N`, il numero totale di persone valutate.

> [!info] Il "positivo" è l'evento che ti interessa
> "Positivo" non vuol dire "buono". È la classe che il modello cerca: il cliente che compra, la frode, il malato, il reddito alto.

### Con la cross-validation

Senza un validation separato, la matrice si costruisce dalla [[Validazione#Cross-validation|cross-validation]]. Ogni persona riceve la sua probabilità dal giro in cui **stava nella fetta di verifica**, quindi da un modello che non l'aveva vista. Da quelle probabilità, con la soglia, nasce la **matrice di confusione cross-validata**.

Ci sono due modi di arrivare a una matrice sola:

| modo | come | quando serve |
|---|---|---|
| **media** | fai una matrice per ogni giro e ne fai la media, cella per cella | è quello che fa `caret` con `confusionMatrix(modello)` |
| **voto** | se ogni persona è stata prevista più volte (cross-validation ripetuta), conti **quante volte** è uscita positiva e quante negativa, e vince la maggioranza | quando i giri si sovrappongono |

---

## Criterio 1: contare le persone

Un esempio che accompagna tutta la sezione. Un modello prevede chi comprerà un prodotto, su **778 clienti**.

| | **ha comprato** | **non ha comprato** | totale |
|---|---|---|---|
| **previsto: compra** | VP = **252** | FP = **211** | 463 |
| **previsto: non compra** | FN = **119** | VN = **196** | 315 |
| totale | 371 | 407 | 778 |

### Le misure valide per qualsiasi target

| misura | formula | nell'esempio |
|---|---|---|
| **accuratezza** (*accuracy*) | `(VP + VN) / N` | `448 / 778` = **0,576** |
| **tasso di errore** (*error rate*, *misclassification rate*) | `1 − accuratezza` | **0,424** |

Il tasso di errore ha anche una seconda scrittura, che mostra da dove viene:

```
error rate = (errore sulla classe 1) × π₁  +  (errore sulla classe 2) × π₂
```

`π` (pi greco) qui è il **prior**, la quota di ciascuna classe nella popolazione. L'errore di una classe è la quota dei suoi membri messi nella classe sbagliata.

> [!example] Stesso esempio, due prior
> Errore sui compratori: `119 / 371` = 0,321. Errore sui non compratori: `211 / 407` = 0,518.
>
> - con i **prior del campione** (371/778 e 407/778) torna **0,424**, cioè `1 − accuratezza`
> - con **prior uguali** (0,5 e 0,5) diventa la **media semplice** dei due errori: `(0,321 + 0,518) / 2` = **0,420**
>
> Il tasso di errore **dipende dai prior**. Se la popolazione vera ha proporzioni diverse dal campione, cambia.

Il tasso di errore stima il [[Classificatore di Bayes#Bayes error rate|Bayes error rate]]: l'errore atteso di classificazione.

### Le misure valide solo per target binari

**Misure di bontà** — più alte = meglio:

| misura | formula | risponde a | nell'esempio |
|---|---|---|---|
| **sensibilità** (*recall*, *true positive rate*, *hit rate*) | `VP / (VP + FN)` | dei compratori veri, quanti ne ho trovati? | `252 / 371` = **0,679** |
| **specificità** (*true negative rate*) | `VN / (VN + FP)` | dei non compratori veri, quanti ne ho riconosciuti? | `196 / 407` = **0,482** |
| **precisione** (*precision*) | `VP / (VP + FP)` | di quelli che ho segnalato, quanti erano giusti? | `252 / 463` = **0,544** |
| **F** | `2·VP / (2·VP + FP + FN)` | un numero solo che combina precisione e recall | `504 / 834` = **0,604** |

**Misure di errore** — più basse = meglio:

| misura | formula | nell'esempio |
|---|---|---|
| **tasso di falsi positivi** (*false positive rate*, *false alarm rate*) | `FP / (FP + VN)` = `1 − specificità` | **0,518** |
| **tasso di falsi negativi** (*false negative rate*) | `FN / (FN + VP)` = `1 − sensibilità` | **0,321** |

> [!tip] Sensibilità e precisione guardano da due lati
> La **sensibilità** parte dalla realtà: fra chi **ha comprato davvero**, quanti ne ho presi.
>
> La **precisione** parte dalla previsione: fra quelli a cui **ho detto "comprerai"**, quanti avevano ragione.
>
> La precisione dà più peso agli eventi positivi: serve quando ogni segnalazione ha un costo, e vuoi che non sia sprecata.

> [!info] Perché F e non la media semplice
> F è la **media armonica** di precisione e recall. La media armonica crolla se uno dei due è basso: precisione 1 e recall 0 danno F = 0, non 0,5.
>
> Così un modello non può "comprare" un buon F eccellendo su una sola delle due.

### Da cosa dipendono

Tutte queste misure dipendono da due scelte:

- la **soglia**: cambiala e cambia tutta la matrice
- i **prior**: se correggi le probabilità con le proporzioni vere della popolazione, la matrice cambia. **Sensibilità e specificità no**: guardano una classe vera alla volta, quindi non risentono di quanto è grande ciascuna classe

### Con più di due classi

Sensibilità, specificità e precisione non hanno più un "positivo" a cui riferirsi. Restano due misure:

- **accuratezza**
- **tasso di errore**, come media degli errori di ciascuna classe pesata con i prior

> [!example] Tre classi
> 100 persone, 80 classificate giuste: accuratezza **0,80**.
>
> Errore per classe: 0, 0,33, 0,43. Con prior uguali il tasso di errore è la media: `(0 + 0,33 + 0,43) / 3` ≈ **0,25**.
>
> Nota: 0,25 e non `1 − 0,80 = 0,20`. Con prior uguali ogni classe pesa uguale, qualunque sia la sua dimensione nel campione.

---

## Criterio 2: contare i soldi

Ogni decisione ha un effetto economico. Scrivere a un cliente costa un francobollo. Se risponde, porta una donazione. Se non risponde, il francobollo è perso.

### La matrice di profitto

Ha la stessa forma della matrice di confusione. In ogni cella c'è **quanto vale** quel caso:

- **sulla diagonale** (i giusti): un **profitto**, di solito positivo
- **fuori diagonale** (gli errori): un **costo**, cioè un profitto negativo

> [!example] La lettera per la raccolta fondi
> - scrivi a chi **risponde**: dona in media 15,54 $, meno 0,68 $ di francobollo = **+14,86 $**
> - scrivi a chi **non risponde**: perdi il francobollo = **−0,68 $**
> - **non scrivi**: nessun costo e nessun guadagno = **0**
>
> | | **risponde** | **non risponde** |
> |---|---|---|
> | **scrivo** | +14,86 | −0,68 |
> | **non scrivo** | 0 | 0 |

### Profitto totale e medio

Si moltiplica **ogni cella della matrice di confusione per la cella corrispondente della matrice di profitto**, e si somma tutto.

```
profitto totale = VP × profitto(VP) + FP × profitto(FP) + FN × profitto(FN) + VN × profitto(VN)

profitto medio  = profitto totale / N
```

> [!example] Con i numeri
> Sul validation il modello dice "scrivi" a 460 persone: 60 rispondono (VP), 400 no (FP).
>
> ```
> profitto totale = 60 × 14,86  −  400 × 0,68  =  891,60 − 272  =  619,60 $
> ```
>
> Le celle "non scrivo" valgono zero, quindi non contribuiscono.

> [!warning] Profitto e accuratezza possono non essere d'accordo
> Il modello più accurato **non è per forza** quello che rende di più. Nell'esempio, un errore "scrivo a chi non risponde" costa 0,68 $, mentre un errore "non scrivo a chi avrebbe risposto" fa perdere 14,86 $.
>
> Conviene un modello che scrive a molti, anche sbagliando spesso. L'accuratezza lo boccerebbe.

> [!info] La matrice di default
> Se non specifichi nessuna matrice, i software ne usano una di default: **1 sulla diagonale, 0 fuori**. Ogni giusto vale 1, ogni errore vale 0.
>
> Con quella, il profitto totale è **il numero di classificati giusti**. Il criterio 2 torna a coincidere con il criterio 1.

> [!question] La scelta da difendere: la misura, e la matrice di profitto sì o no
> **Cosa scegli:** con quale numero giudichi il modello. Accuratezza, sensibilità, precisione, F, oppure il profitto.
>
> **Che problema risolve:** di **business**. La misura dice cosa conta per chi usa il modello: trovare tutti i compratori (sensibilità), non sprecare contatti (precisione), guadagnare (profitto).
>
> **Cosa ti costa:** non calcolare la matrice di profitto **è comunque una scelta**. Usi quella di default e assumi che **ogni errore costi uguale**. Si può fare, per esempio quando i costi non si conoscono. Ma va detto.
>
> **All'esame:** *"Non ho una matrice di profitto perché nessuno sa quanto costa un contatto sprecato. Assumo costi uguali e lo dichiaro. Visto che perdere un compratore mi preoccupa di più, guardo la sensibilità, non l'accuratezza."* → [[Il metodo]]

### Il profitto atteso di una persona

Le misure sopra riassumono tutto il dataset. Si può anche calcolare, **per ogni persona**, il profitto atteso di ciascuna decisione. Si chiama **expected profit**, `EP`.

Serve la probabilità `p` che la persona sia un positivo:

```
EP(sì) = p × profitto(VP) + (1 − p) × profitto(FP)
EP(no) = p × profitto(FN) + (1 − p) × profitto(VN)
```

**Si sceglie la decisione con l'EP più alto.**

> [!example] La spedizione del catalogo
> Se il cliente compra, guadagni **18 $**. Se non compra, hai speso **4 $** di spedizione. Il modello gli dà `p = 0,6`.
>
> ```
> EP(spedisco)    = 0,6 × 18  +  0,4 × (−4)  =  10,8 − 1,6  =  9,20 $
> EP(non spedisco) = 0
> ```
>
> 9,20 > 0: **si spedisce**.

> [!important] Con la matrice di default, EP = probabilità
> Con 1 sulla diagonale e 0 fuori: `EP(sì) = p` e `EP(no) = 1 − p`.
>
> Scegliere l'EP più alto diventa scegliere **la probabilità più alta**. È la regola del [[Classificatore di Bayes#La regola di decisione|max posterior]]. La regola basata sui profitti la **contiene** come caso particolare.

Dalla regola dell'EP si ricava anche la **soglia ottimale**: la probabilità oltre la quale conviene dire "sì" (→ [[Scegliere la soglia#Se i due errori costano diverso]]).

### Cosa cambia quando usi una matrice di profitto

| cosa | senza matrice | con matrice |
|---|---|---|
| **la previsione** | vince la probabilità più alta | vince l'**EP** più alto |
| **il confine di decisione** | quello del max posterior | si sposta |
| **le variabili utili** | quelle significative | quelle legate a costi e profitti |
| **il giudizio sul modello** | statistiche di classificazione | **profitto totale** |

---

## Gli eventi rari

Un evento è **raro** quando la sua classe è sotto l'**1%** della popolazione. Frodi, insolvenze gravi, guasti.

> [!warning] Il modello che dice sempre "no"
> Con l'1% di eventi, un modello che risponde "no" a tutti ha accuratezza **99%**. E non trova un solo evento (→ [[Scegliere la soglia#Il problema degli eventi rari]]).

Due soluzioni:

**1. Una matrice di profitto che premia l'evento raro.** Dichiari che classificare giusto un caso raro vale **20 volte** un caso comune: 20 sulla sua cella di diagonale, 1 sulle altre. Il modello che massimizza il profitto ora ha un motivo per cercarli. Il profitto totale diventa il criterio per scegliere il modello.

**2. Ribilanciare il dataset.**

| tecnica | cosa fa |
|---|---|
| **undersampling** | tieni tutti i rari e **solo una parte** dei comuni |
| **oversampling** | **moltiplichi** i casi rari, estraendoli più volte |

Il dataset arriva a 50/50, o 70/30. Il modello impara meglio a riconoscere i rari.

> [!warning] Dopo il ribilanciamento, i prior veri
> Il dataset bilanciato **non rappresenta più la popolazione**. Il modello crede che gli eventi siano il 30%, quando sono l'1%, e tutte le sue probabilità sono gonfiate.
>
> Vanno dichiarati i **prior veri** e corrette le probabilità (→ [[Scegliere la soglia#Il prior sbagliato]]). Vale per ogni dataset non rappresentativo: filtrato su un solo periodo, già bilanciato da qualcun altro.

Cambiare i prior cambia la matrice di confusione, i profitti e tutte le misure. **Tranne sensibilità e specificità.**

> [!question] La scelta da difendere: ribilanciare o no
> **Cosa scegli:** se fare undersampling del training.
>
> **Che problema risolve:** **statistico**. I positivi sono troppo pochi perché l'algoritmo impari, o i negativi sono così tanti da costare solo tempo di calcolo.
>
> **Cosa ti costa:** butti informazione, e le probabilità escono **gonfiate**: vanno corrette col prior vero. Si ribilancia **solo il training, dentro ogni fold**. Validation e test restano con la distribuzione vera.
>
> **Occhio:** se il problema è *"dice no a tutti"*, spesso non serve ribilanciare. Serve una **soglia** più bassa, scelta con i costi.
>
> **All'esame:** *"Ho confrontato in cross-validation il modello con e senza undersampling, su fold con la distribuzione vera, usando l'AUC. Con undersampling va meglio, quindi lo tengo e correggo le probabilità."* → [[Il metodo#Un esempio completo: l'undersampling]]

---

## In R

Sul dataset **adult**: l'evento è `H`, reddito sopra 50.000 $.

```r
library(caret)

prev <- predict(m, newdata = test.df)          # le classi previste

cm <- confusionMatrix(prev, test.df$incometgt,
                      positive = "H",          # l'evento che ti interessa
                      mode = "everything")     # aggiunge precision, recall, F1
cm
cm$byClass[c("Sensitivity", "Specificity", "Precision", "Recall", "F1")]
```

La matrice di confusione **cross-validata**, dall'oggetto di `train()`:

```r
confusionMatrix(m)   # media delle celle sui giri, in percentuale
```

Il **profitto totale**: si moltiplicano le due matrici cella per cella e si somma.

```r
tab <- cm$table      # righe = previsto (L, H), colonne = osservato (L, H)

# ipotesi: a chi prevedo H mando un'offerta.
# se e' davvero H guadagno 20, se e' L perdo 2. Se prevedo L non faccio niente.
profitto <- matrix(c( 0,  0,     # previsto L
                     -2, 20),    # previsto H
                   nrow = 2, byrow = TRUE, dimnames = dimnames(tab))

sum(tab * profitto)                  # profitto totale
sum(tab * profitto) / sum(tab)       # profitto medio per persona
```

> [!warning] L'ordine di righe e colonne
> In `caret` le **righe** sono il previsto e le **colonne** l'osservato. La matrice di profitto va scritta **nello stesso ordine**, altrimenti moltiplichi il profitto di una cella per il conteggio di un'altra. `dimnames = dimnames(tab)` serve a controllarlo a colpo d'occhio.

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| Le due strade? | contare le **persone** o contare i **soldi** |
| Cosa serve per la matrice di confusione? | probabilità **sul validation** + una **soglia** |
| E con la cross-validation? | la matrice **cross-validata**: media dei giri, o voto |
| **Accuratezza**? | `(VP + VN) / N` |
| **Error rate**? | `1 − accuratezza`. Media degli errori di classe **pesata coi prior** |
| **Sensibilità** / **recall**? | `VP / (VP + FN)`: dei veri sì, quanti presi |
| **Specificità**? | `VN / (VN + FP)`: dei veri no, quanti riconosciuti |
| **Precisione**? | `VP / (VP + FP)`: dei segnalati, quanti giusti |
| **F**? | `2VP / (2VP + FP + FN)`: media armonica di precisione e recall |
| **FPR**? | `1 − specificità` |
| Più di due classi? | restano solo **accuratezza** ed **error rate** |
| Cosa non cambia coi prior? | **sensibilità e specificità** |
| **Profitto totale**? | somma di conteggio × valore, cella per cella |
| **EP** di una persona? | `p × valore se sì + (1 − p) × valore se no`. Vince l'EP più alto |
| Matrice di default? | 1 sulla diagonale: EP = probabilità, torna il max posterior |
| **Evento raro**? | sotto l'**1%** |
| Le due cure? | premiarlo nella **matrice di profitto**, o **ribilanciare** e poi rimettere i prior veri |

## Vedi anche

[[Confrontare i modelli]] · [[Scegliere la soglia]] · [[Validazione]] · [[Classificatore di Bayes]] · [[Machine Learning]] · [[R]]
