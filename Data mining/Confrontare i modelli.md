---
date: 2026-09-25
tags: [data-mining]
status: active
image: "[[assets/covers/data-mining.svg]]"
area: data-mining
---

# Confrontare i modelli

**In una riga:** per scegliere fra più modelli servono misure che **non dipendono dalla soglia**. Si chiama **assessment**.

Hai addestrato una logistica, un albero e un kNN. Quale tieni? Questa è la domanda. Come si giudica poi il modello scelto sta in [[Valutare un classificatore]].

> [!warning] Mai l'error rate per confrontare modelli
> Accuratezza, error rate, sensibilità, precisione, profitto totale: si calcolano **tutte** sulla matrice di confusione. E la matrice di confusione esiste solo **dopo aver scelto una soglia**.
>
> Cambi soglia, cambiano tutte. Il modello A può battere B a soglia 0,5 e perdere a soglia 0,3.
>
> I software usano 0,5 di default. Ma non c'è nessun motivo per cui 0,5 sia il punto giusto in cui confrontare. **Si confrontano i modelli su tutte le soglie insieme**, o con misure che la soglia non la usano proprio.

---

## Le quattro misure di assessment

| misura | cosa guarda | target |
|---|---|---|
| **1. ASE sul validation** | quanto le probabilità stanno lontane dalla realtà | tutti (per più classi: **Brier score**) |
| **2. curva ROC e AUC** | tutte le soglie insieme | **solo binari** |
| **3. lift chart** | quanto il modello concentra gli eventi in cima alla classifica | qualitativi |
| **4. profitto calcolato** | quanto rende il modello, con una matrice di profitto | tutti |

> [!tip] Quale usare, in pratica
> 1. prima la **ROC**
> 2. se le curve **si incrociano**, il **lift chart**
> 3. l'ASE si usa di rado
> 4. il **profitto**, quando interessa l'impatto economico
>
> Con più di due classi la ROC non esiste: restano Brier score, lift e profitto.

---

## 1. ASE sul validation

**ASE** = *average squared error*, errore quadratico medio. Per ogni persona confronti la sua classe vera (`y` = 1 o 0) con la probabilità stimata (`p`), elevi al quadrato, e fai la media.

```
ASE = media di (y − p)²
```

> [!example] Tre persone
> | persona | vero `y` | stimato `p` | `(y − p)²` |
> |---|---|---|---|
> | 1 | 1 | 0,9 | 0,01 |
> | 2 | 0 | 0,2 | 0,04 |
> | 3 | 1 | 0,4 | 0,36 |
>
> ASE = `(0,01 + 0,04 + 0,36) / 3` = **0,137**. La terza persona pesa quasi tutto: era un sì, e il modello le dava solo 0,4.

Non usa nessuna soglia: guarda la **distanza** fra probabilità e realtà. È più una misura di **bontà di adattamento** che di classificazione. Calcolata in cross-validation si chiama **ASE cross-validato**.

### Per più classi: il Brier score

Con tre classi, ogni persona ha **tre probabilità** e una sola classe vera. Si trasforma la classe vera in tre colonne 0/1 (le [[Preprocessing#Le variabili categoriali|dummy]]) e si sommano i tre scarti al quadrato.

> [!example] Una persona, tre classi
> È della classe 1. Il modello le dà 0,342, 0,379, 0,278.
>
> ```
> (1 − 0,342)² + (0 − 0,379)² + (0 − 0,278)²  =  0,433 + 0,144 + 0,077  =  0,654
> ```
>
> Si ripete per tutte le persone e si fa la media.

---

## 2. Curva ROC e AUC

La curva ROC prova **tutte le soglie** e per ognuna segna un punto: sensibilità in verticale, `1 − specificità` in orizzontale. L'**AUC** è l'area sotto la curva: 0,5 = a caso, 1 = perfetto. La spiegazione completa sta in [[Scegliere la soglia#La curva ROC]].

Qui interessa un fatto: sensibilità e specificità **non cambiano con i prior**. La ROC confronta i modelli senza dipendere né dalla soglia né da quanto è raro l'evento.

> [!example] L'AUC contata a mano
> Dieci persone, cinque "+" e cinque "−". Il modello M1 dà queste probabilità di "+":
>
> - i cinque "+": 0,73 · 0,69 · 0,67 · 0,47 · 0,45
> - i cinque "−": 0,44 · 0,55 · 0,08 · 0,15 · 0,35
>
> L'AUC è la quota di **coppie (+, −)** in cui il "+" ha la probabilità più alta. Le coppie sono `5 × 5 = 25`.
>
> - 0,73, 0,69 e 0,67 battono tutti e cinque i "−": 15 coppie
> - 0,47 e 0,45 battono quattro "−" su cinque (perdono contro 0,55): 8 coppie
>
> AUC = `23 / 25` = **0,92**.
>
> Un secondo modello M2, sulle stesse persone, arriva a **0,40**: peggio che tirare a caso. **Si tiene M1.**

### Quando le curve si incrociano

A volte una curva sta sopra l'altra **ovunque**: nessun dubbio, vince quella.

Altre volte si incrociano. M1 è migliore dove i falsi allarmi sono pochi, M2 dove sono tanti. Nessuno dei due domina.

In quel caso si guarda solo la **zona di soglie che ti interessa davvero**. Ed è il lavoro del lift chart.

---

## 3. Lift chart

La domanda del marketing: **se contatto solo il 10% dei clienti, quanti dei compratori prendo?**

### Come si costruisce

1. stimi sul validation la probabilità di evento di ogni persona
2. **ordini** le persone dalla probabilità più alta alla più bassa
3. le dividi in **10 decili**: il primo decile è il 10% più propenso, l'ultimo il 10% meno propenso
4. in ogni decile **conti gli eventi veri**

Se il modello funziona, gli eventi si ammassano nei primi decili.

### Le quattro misure

L'esempio: mandi **1000 email**, rispondono **200** persone. La probabilità globale di risposta è `200 / 1000` = **20%**. Nel primo decile (100 persone) rispondono in **55**.

| misura | formula | primo decile |
|---|---|---|
| **% response** | successi del decile / persone del decile | `55 / 100` = **55%** |
| **% captured response** | successi del decile / successi totali | `55 / 200` = **27,5%** |
| **lift** | % response del decile / % globale | `55% / 20%` = **2,75** |
| **gain** | (% response − % globale) / % globale | `(55 − 20) / 20` = **175%** |

> [!important] Come si legge il lift
> **Lift = 2,75** vuol dire: nel primo decile trovi **2,75 volte** i rispondenti che troveresti scegliendo a caso.
>
> Lift = 1 vuol dire che il modello non serve: quel decile è come un campione casuale.

### Le versioni cumulate

Di solito si guardano **cumulate**: il primo decile, poi i primi due insieme, poi i primi tre.

> [!example] La lettura tipica
> *"Contattando il 60% dei clienti con la probabilità più alta, il modello cattura il 75% dei compratori."*
>
> È la **% captured response cumulata** al sesto decile. Si confrontano i modelli guardando quale curva sale più in fretta.

Il lift chart non ha bisogno di una soglia fissa, e funziona anche con target a più classi.

---

## 4. Il profitto calcolato

Se hai una [[Valutare un classificatore#Criterio 2: contare i soldi|matrice di profitto]], questa è **la misura migliore** per confrontare modelli.

### Profitto medio calcolato

C'è una differenza sottile con il profitto totale della valutazione.

- lì si parte dalla **matrice di confusione**: ogni persona classificata con la probabilità e una soglia
- qui si parte dalla **tabella di decisione**: ogni persona classificata con il suo **EP** più alto

A ogni persona si assegna il profitto che la sua decisione produce davvero, dato il suo valore vero. Il **profitto medio calcolato** è la media di quella colonna.

> [!example] Con i numeri
> Profitto di 20 se prevedi "sì" a un sì vero, −2 se prevedi "sì" a un no vero, 0 se prevedi "no".
>
> Sul validation (1374 persone) il modello decide "sì" per 689 sì veri e 644 no veri:
>
> ```
> profitto totale calcolato = 689 × 20  +  644 × (−2)  =  13.780 − 1.288  =  12.492
> profitto medio calcolato  = 12.492 / 1374 ≈ 9,09 a persona
> ```
>
> Vince il modello con il profitto medio più alto.

### Il lift del profitto

Stessa costruzione del lift chart, ma in ogni decile si calcola il **profitto atteso** invece del numero di eventi.

Il grafico cumulato risponde a: *"se contatto il primo 40%, quanto guadagno in tutto?"*. Il punto in cui la curva cumulata smette di salire è la quota di clienti oltre la quale **contattare altri costa più di quanto rende**.

---

## In R

Sul dataset **adult**, confronto fra una logistica e un albero. L'evento è `H`.

```r
library(pROC)

p_log <- predict(m_log,   newdata = test.df, type = "prob")[, "H"]
p_alb <- predict(m_alber, newdata = test.df, type = "prob")[, "H"]

roc_log <- roc(test.df$incometgt, p_log, levels = c("L", "H"))
roc_alb <- roc(test.df$incometgt, p_alb, levels = c("L", "H"))

auc(roc_log); auc(roc_alb)
plot(roc_log); lines(roc_alb, col = "red")   # si incrociano?

# ASE (Brier score per due classi)
y <- as.numeric(test.df$incometgt == "H")
mean((y - p_log)^2)
```

Il lift per decili, costruito a mano:

```r
library(dplyr)

decili <- data.frame(p = p_log, evento = y) |>
  mutate(decile = ntile(-p, 10)) |>             # 1 = le probabilita' piu' alte
  group_by(decile) |>
  summarise(n = n(), successi = sum(evento)) |>
  mutate(response = successi / n,
         globale  = sum(successi) / sum(n),
         lift     = response / globale,
         captured_cum = cumsum(successi) / sum(successi))

decili
```

> [!tip] `ntile(-p, 10)`
> `ntile` divide in 10 gruppi uguali dal valore più **piccolo** al più grande. Il segno meno ribalta l'ordine: il decile 1 diventa quello con le probabilità più alte.

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| Cos'è l'**assessment**? | il confronto **fra modelli**, prima di sceglierne uno |
| Perché non l'error rate? | dipende dalla **soglia**: cambia soglia, cambia verdetto |
| Le quattro misure? | **ASE**, **ROC/AUC**, **lift**, **profitto calcolato** |
| In che ordine? | ROC · se si incrociano, lift · profitto se conta l'impatto economico |
| **ASE**? | media di `(y − p)²`. Nessuna soglia |
| **Brier score**? | l'ASE per più classi |
| La ROC con più classi? | **non esiste** |
| **Lift** del decile? | quante volte meglio del caso. **1 = inutile** |
| **% captured response**? | quota di tutti gli eventi che cade in quel decile |
| **Gain**? | `(response − globale) / globale` |
| Profitto calcolato? | profitto delle decisioni prese con l'**EP**, non con la soglia |

## Vedi anche

[[Valutare un classificatore]] · [[Scegliere la soglia]] · [[Validazione]] · [[kNN]] · [[Alberi decisionali]] · [[R]]
