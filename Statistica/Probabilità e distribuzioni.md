---
date: 2026-09-19
tags: [statistica]
status: active
image: "[[assets/covers/statistica.svg]]"
area: statistica
---

# Probabilità e distribuzioni

**In una riga:** le regole del caso, e i modelli già pronti per i fenomeni che si ripetono.

È il ponte fra le due metà del corso. La [[Statistica descrittiva|descrittiva]] guarda i dati che hai; l'[[Inferenza|inferenza]] scommette su quelli che non hai. Per scommettere in modo sensato serve saper misurare il caso.

---

## Probabilità

**Un numero fra 0 e 1 che dice quanto è plausibile che una cosa succeda.**

| valore | significato |
|---|---|
| **0** | non succede mai |
| **0,5** | tanto sì quanto no |
| **1** | succede sempre |

Si scrive `P(qualcosa)`. Quindi `P(esce testa) = 0,5`.

### Da dove viene il numero

Due strade.

**Contando i casi**, quando sono tutti equiprobabili:

```
                casi che mi interessano
probabilità = ─────────────────────────
                  casi possibili
```

Un dado: `P(esce 6) = 1/6`. `P(esce un numero pari) = 3/6 = 0,5`.

**Osservando la frequenza**, quando i casi non sono equiprobabili o non li puoi contare:

> [!info] L'impostazione frequentista
> Non sai se una puntina da disegno lanciata cade con la punta in su o di lato. Non c'è simmetria da sfruttare, quindi non puoi contare niente.
>
> La lanci **mille volte** e vedi che 380 cadono con la punta in su. Dici `P ≈ 0,38`.
>
> **La probabilità è la frequenza relativa quando le prove diventano tantissime.** È il modo in cui la probabilità entra nei dati reali: la quota di clienti che non paga, la quota di pezzi difettosi.

E qui c'è già il collegamento con il resto: quella quota osservata è il **prior** del [[Classificatore di Bayes]].

### Le poche regole che servono

| regola | quando | esempio |
|---|---|---|
| `P(non A) = 1 − P(A)` | sempre | se piove con probabilità 0,3, non piove con 0,7 |
| `P(A e B) = P(A) × P(B)` | solo se sono **indipendenti** | due dadi che fanno entrambi 6: `1/6 × 1/6 = 1/36` |
| `P(A o B) = P(A) + P(B)` | solo se **non possono capitare insieme** | il dado fa 1 **o** 2: `1/6 + 1/6 = 1/3` |

> [!warning] "Indipendenti" è una condizione, non un dettaglio
> Due eventi sono indipendenti se **sapere di uno non cambia la probabilità dell'altro**.
>
> Due lanci di dado: indipendenti. Il dado non ricorda niente.
>
> Pioggia oggi e pioggia domani: **non** indipendenti. Moltiplicare le probabilità darebbe un risultato sbagliato.
>
> È la stessa assunzione che il [[Classificatore di Bayes#Naive Bayes|Naive Bayes]] fa apposta pur sapendo che è falsa.

---

## Variabile casuale

> **Una variabile il cui valore lo decide il caso.**

Lanci due dadi e sommi. Il risultato è un numero fra 2 e 12, e non sai quale finché non lanci. Quello è una variabile casuale.

La cosa utile non è il singolo lancio: è sapere **quanto è probabile ciascun risultato**. Quell'elenco si chiama **distribuzione**.

| somma | 2 | 3 | 4 | 5 | 6 | **7** | 8 | 9 | 10 | 11 | 12 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| probabilità su 36 | 1 | 2 | 3 | 4 | 5 | **6** | 5 | 4 | 3 | 2 | 1 |

Il 7 è il più probabile perché ci sono sei modi di ottenerlo, il 2 uno solo. Disegnata, questa tabella fa già una specie di campana.

### Discrete e continue

| | **discreta** | **continua** |
|---|---|---|
| valori | separati, si contano | qualsiasi, si misurano |
| esempi | quanti figli, quanti difetti | altezza, tempo, peso |
| si descrive con | **funzione di probabilità**: `P(X = 3) = 0,2` | **densità**: le probabilità stanno nell'**area** |

> [!info] Perché su una continua `P(X = valore esatto)` è zero
> Qual è la probabilità che una persona sia **esattamente** alta 175,000000… cm? Zero: c'è sempre un altro decimale.
>
> Su una variabile continua hanno senso solo le domande su **intervalli**: *"fra 170 e 180?"*. E la risposta è l'**area** sotto la curva fra quei due punti.
>
> Per questo sulle continue si parla di **densità** e non di probabilità punto per punto.

### Media e varianza di una variabile casuale

Gli stessi due numeri di sempre, calcolati però sulla distribuzione teorica invece che sui dati:

- **valore atteso** `E(X)` o `μ` — il valore medio **a lungo andare**. Su un dado è 3,5, che non uscirà mai in un lancio singolo
- **varianza** `σ²` — quanto i risultati si sparpagliano attorno a quel centro

Sono gli stessi concetti di [[Valori medi]] e [[Variabilità]], spostati dal "ho misurato" al "mi aspetto".

---

## I modelli discreti

Alcune situazioni si ripetono così spesso che hanno una formula già pronta. Le principali:

### Bernoulli — una prova sola, due esiti

**Un solo tentativo, che può andare bene o male.** Testa o croce, il cliente compra o non compra, lo studente fuma o non fuma.

Un solo parametro: `p`, la probabilità di successo.

> [!tip] L'hai già incontrata
> È il mattone della [[Regressione logistica#La verosimiglianza L, calcolata a mano|verosimiglianza]]: ogni persona del dataset è una Bernoulli, e la verosimiglianza è il prodotto di tutte.

### Binomiale — la stessa prova ripetuta n volte

**Quante volte va bene su `n` tentativi indipendenti**, tutti con la stessa probabilità `p`.

> [!example] Il controllo qualità
> Il 2% dei pezzi è difettoso. Ne prendi 100. Quanti difettosi ti aspetti, e quanto è probabile trovarne più di 5?
>
> È una binomiale con `n = 100` e `p = 0,02`. In media ne trovi `n × p = 2`.

Le condizioni da controllare: numero di prove **fisso**, esiti **due soli**, prove **indipendenti**, probabilità **costante**. Se una salta, la binomiale non è il modello giusto.

### Poisson — quanti eventi in un intervallo

**Quante volte capita un evento raro in un certo tempo o spazio**, quando non c'è un "numero di prove" da contare.

Chiamate al centralino in un'ora. Errori di battitura per pagina. Clienti che entrano in negozio in dieci minuti.

Un solo parametro: `λ` (lambda), il numero medio di eventi nell'intervallo.

> [!info] Binomiale o Poisson?
> - **binomiale**: c'è un numero fisso di tentativi. *Su 100 pezzi, quanti difettosi?*
> - **Poisson**: non c'è un tetto. *In un'ora, quante chiamate?* Potrebbero essere 3, 50 o zero
>
> Quando `n` è grande e `p` piccolo, la binomiale somiglia moltissimo a una Poisson con `λ = n × p`.

### Le altre

**Ipergeometrica** — come la binomiale, ma estraendo **senza rimettere dentro**: le probabilità cambiano a ogni estrazione. **Geometrica** — quanti tentativi prima del primo successo. **Multinomiale** — la binomiale con più di due esiti possibili.

Serve riconoscerle, più che saperle a memoria. La domanda è sempre la stessa: *quante prove, quanti esiti, con o senza reimmissione?*

---

## La distribuzione Normale

La più importante di tutte. Continua, simmetrica, a **campana**.

```mermaid
flowchart LR
    A["pochi<br/>molto bassi"] --> B["TANTI<br/>attorno alla media"] --> C["pochi<br/>molto alti"]
```

Due soli parametri, e fanno tutto:

- **`μ` (media)** — dove sta il centro. Sposta la campana a destra o a sinistra
- **`σ` (deviazione standard)** — quanto è larga. Grande = campana bassa e spanciata, piccola = stretta e appuntita

Si scrive `X ~ N(μ, σ²)`, e la tilde si legge **"si distribuisce come"**.

### La regola che devi sapere a memoria

Sotto una campana, **ovunque sia il centro e qualunque sia la larghezza**:

| entro… | ci sta il… |
|---|---|
| **1** deviazione standard dalla media | **68%** dei casi |
| **2** deviazioni standard | **95%** dei casi |
| **3** deviazioni standard | **99,7%** dei casi |

> [!example] Applicata subito
> Altezze con media 170 cm e deviazione standard 7 cm:
>
> - fra 163 e 177 cm → **68 persone su 100**
> - fra 156 e 184 cm → **95 su 100**
> - fra 149 e 191 cm → **99,7 su 100**
>
> Uno sopra i 191 cm è uno su trecento circa.

Quel **95% entro due deviazioni standard** è esattamente da dove esce il "± 2 errori standard" dell'[[Inferenza#Intervallo di confidenza|intervallo di confidenza]]. Non è una coincidenza: è la stessa proprietà, usata due volte.

### La standardizzazione

Problema: di campane ce n'è un'infinità, una per ogni coppia `μ` e `σ`. Non puoi avere una tavola per ciascuna.

Soluzione: **riportarle tutte alla stessa**, con una trasformazione che si chiama standardizzazione.

```
      valore − media
z = ──────────────────
     deviazione standard
```

Il risultato `z` si legge: **"quante deviazioni standard sono lontano dal centro"**.

> [!example] Chi è andato meglio?
> - Marco: **82** a un esame con media 70 e deviazione 6
> - Anna: **45** a un esame con media 38 e deviazione 3
>
> I voti grezzi non si confrontano: sono due scale diverse.
>
> ```
> z Marco = (82 − 70) / 6 = 2,00
> z Anna  = (45 − 38) / 3 = 2,33
> ```
>
> **Anna.** È più lontana dalla media dei suoi compagni di quanto Marco lo sia dai suoi.

La campana standardizzata ha sempre **media 0 e deviazione standard 1**, e si scrive `N(0,1)`. È per questa, e solo per questa, che esistono le tavole stampate: standardizzi, e poi leggi lì.

| z | significa |
|---|---|
| 0 | esattamente sulla media |
| +1 | una deviazione standard sopra |
| ±1,96 | i confini del **95% centrale** ← il numero dietro gli intervalli di confidenza |
| ±2,58 | i confini del **99%** |

### Quando la Normale non c'è

Molti metodi la danno per buona. Vale la pena controllare:

- **guarda l'istogramma** — è a campana o pende da una parte?
- **confronta media e mediana** — molto diverse significa distribuzione storta (→ [[Variabilità#Asimmetria]])
- **il grafico Q-Q** — se i punti stanno su una diagonale, la normalità regge

> [!important] Il salvagente
> Anche quando i dati non sono normali, **la media campionaria lo diventa lo stesso** se il campione è abbastanza grande. È il [[Inferenza#Il teorema del limite centrale|teorema del limite centrale]], ed è il motivo per cui la maggior parte dei metodi continua a funzionare.

---

## In R

```r
# la Normale: d=densita'  p=probabilita' cumulata  q=quantile  r=numeri casuali
dnorm(0)                      # l'altezza della curva in 0
pnorm(1.96)                   # quanta area c'e' a sinistra di 1.96 -> 0.975
qnorm(0.975)                  # il contrario: quale z lascia a sinistra il 97,5% -> 1.96
rnorm(100, mean = 170, sd = 7)  # 100 altezze finte

# standardizzare una variabile
z <- (dati$voto - mean(dati$voto)) / sd(dati$voto)
z <- scale(dati$voto)         # stessa cosa, gia' pronta

# le altre distribuzioni seguono la stessa logica: d/p/q/r + nome
dbinom(3, size = 10, prob = 0.2)   # esattamente 3 successi su 10
pbinom(3, size = 10, prob = 0.2)   # al massimo 3 successi
dpois(2, lambda = 4)               # esattamente 2 eventi, con media 4

# controllare la normalita'
hist(dati$voto)
qqnorm(dati$voto); qqline(dati$voto)
```

> [!tip] Le quattro lettere davanti
> Valgono per **tutte** le distribuzioni, non solo la Normale:
> **d** = densità · **p** = probabilità cumulata (area a sinistra) · **q** = quantile (il contrario di p) · **r** = estrai numeri a caso.
>
> Imparata la logica su `norm`, funziona identica su `binom`, `pois`, `t`, `chisq`, `f`.

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| Cos'è una probabilità? | un numero **fra 0 e 1** |
| Le due strade per calcolarla? | **contare** i casi, oppure **osservare** la frequenza |
| Quando si moltiplicano? | solo se gli eventi sono **indipendenti** |
| Cos'è una variabile casuale? | una variabile il cui valore lo decide il **caso** |
| Discreta o continua? | si **contano** o si **misurano** |
| `P(X = 175,000…)` su una continua? | **zero**. Hanno senso solo gli intervalli |
| **Bernoulli**? | una prova sola, due esiti |
| **Binomiale**? | quante volte va bene su `n` prove |
| **Poisson**? | quanti eventi in un intervallo, senza tetto |
| I due parametri della Normale? | **media** (dov'è) e **deviazione standard** (quanto è larga) |
| La regola da sapere? | **68 – 95 – 99,7%** entro 1, 2, 3 deviazioni standard |
| Cos'è `z`? | **quante deviazioni standard** sei lontano dal centro |
| Perché standardizzare? | per confrontare scale diverse, e per usare **una sola tavola** |
| E se i dati non sono normali? | con `n` grande **la media** lo diventa comunque |

## Vedi anche

[[Statistica descrittiva]] · [[Valori medi]] · [[Variabilità]] · [[Inferenza]] · [[Classificatore di Bayes]] · [[R]]
