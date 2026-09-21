---
date: 2026-09-19
tags: [data-mining]
status: active
image: "[[assets/covers/data-mining.svg]]"
area: data-mining
---

# Regolarizzazione

**In una riga:** mettere una tassa sui coefficienti, per costringere il modello a essere più semplice.

> [!example] Lo studente che scrive troppo
> Dai un tema e il tuo compagno consegna quaranta pagine: infila tutto, anche quello che non c'entra. Alcune frasi sono giuste, molte sono rumore, e alla fine il tema è peggiore.
>
> Se dici *"massimo due pagine"*, è costretto a tenere solo quello che conta davvero.
>
> La regolarizzazione è quel limite di pagine, applicato a un modello.

---

## Il problema

Un modello con tante variabili si adatta benissimo ai dati che ha visto e male a quelli nuovi: è l'[[Validazione|overfitting]].

Il sintomo si vede nei numeri:

> [!warning] I tre segnali
> - i **coefficienti diventano enormi**, tipo `+4.200` e `−3.850`, e si compensano a vicenda
> - **cambiano completamente** se togli tre osservazioni
> - il modello **fitta benissimo** ma quasi nessuna variabile è significativa presa da sola
>
> Il terzo è anche il sintomo della [[Preprocessing#2. Variabili collineari|collinearità]], e non è un caso: sono la stessa malattia. Quando due variabili dicono la stessa cosa, il modello può dare `+1000` all'una e `−1000` all'altra, ottenere lo stesso risultato, ed essere completamente instabile.

---

## L'idea

Un modello normale cerca i coefficienti che **sbagliano meno possibile** sui dati di training.

La regolarizzazione cambia l'obiettivo:

```
minimizza     errore   +   λ × (quanto sono grandi i coefficienti)
                            └──────── la penalità ────────┘
```

Ora il modello paga un prezzo per ogni coefficiente grande. Ne tiene uno solo se **si ripaga**, cioè se riduce l'errore più di quanto costa.

> [!important] λ è la manopola
> **λ** (lambda) decide quanto è cara la tassa.
>
> | λ | cosa succede |
> |---|---|
> | **0** | nessuna tassa: torni al modello normale |
> | **piccolo** | i coefficienti si restringono un po' |
> | **grande** | i coefficienti vengono schiacciati verso zero |
> | **enorme** | tutti a zero: il modello non dice più niente |
>
> Il valore giusto non si indovina: **si sceglie in [[Validazione#Cross-validation|cross-validation]]**, provandone tanti e tenendo quello che sbaglia meno sui dati nuovi.

---

## Ridge e lasso

Cambia **come si misura** "quanto sono grandi i coefficienti". Due modi, due comportamenti molto diversi.

| | **ridge** | **lasso** |
|---|---|---|
| la penalità usa | i coefficienti **al quadrato** | i coefficienti in **valore assoluto** |
| effetto | li **rimpicciolisce** tutti | ne porta alcuni **esattamente a zero** |
| quante variabili restano | tutte | **solo alcune** |
| fa selezione? | no | **sì** |

> [!important] La differenza che conta
> **Ridge** restringe e basta: un coefficiente può diventare 0,003, ma non zero. Tutte le variabili restano nel modello, con voce più bassa.
>
> **Lasso** invece azzera davvero. E un coefficiente a zero significa **variabile eliminata**.
>
> Quindi il lasso fa due lavori in uno: regolarizza **e sceglie le variabili**, in automatico, senza che tu debba provare combinazioni a mano.

> [!info] Perché il quadrato non azzera e il valore assoluto sì
> Con il quadrato, la penalità per ridurre un coefficiente da 0,01 a 0 è ridicola: non conviene mai completare il viaggio.
>
> Con il valore assoluto la penalità è **costante** per ogni passo, anche l'ultimo. Arrivare a zero conviene ancora, quindi ci arriva.
>
> Geometricamente si dice che il vincolo del lasso ha gli **spigoli**, e la soluzione tende a cascare proprio lì — cioè su punti dove qualche coefficiente vale zero.

### Quando usare quale

| situazione | scelta |
|---|---|
| tante variabili, sospetti che **poche** contino | **lasso** |
| vuoi un modello **più corto** da leggere | **lasso** |
| le variabili contano **tutte un po'** | **ridge** |
| gruppi di variabili molto correlate fra loro | **ridge**, o *elastic net* |

> [!tip] L'elastic net
> È la miscela: un pezzo di penalità ridge e un pezzo di lasso, dosati da un secondo parametro `α`.
>
> `α = 1` è lasso puro, `α = 0` è ridge puro, nel mezzo un misto. Utile quando hai gruppi di variabili correlate: il lasso puro, fra due variabili quasi identiche, ne tiene una a caso e butta l'altra; l'elastic net le tiene entrambe con peso ridotto.

---

## Due cose da non dimenticare

> [!warning] Standardizza prima
> La penalità colpisce i **coefficienti**, e la dimensione di un coefficiente dipende dall'unità di misura della sua variabile. Un reddito in euro ha un coefficiente minuscolo, lo stesso reddito in migliaia di euro ce l'ha mille volte più grande.
>
> Senza standardizzare, la tassa colpisce a caso: alcune variabili vengono penalizzate molto e altre per niente, solo per come sono scritte (→ [[Preprocessing#4. Scaling]]).
>
> I software di solito lo fanno da soli, ma è bene saperlo.

> [!warning] Il lasso sceglie, non spiega
> Il lasso ti dà un elenco di variabili sopravvissute. **Non è una classifica di importanza causale.**
>
> Fra due variabili molto correlate ne tiene una e azzera l'altra, e quale delle due sopravvive può dipendere da poche osservazioni. Cambiando leggermente i dati, cambia la scelta.
>
> Usalo per costruire un modello più snello, non per concludere che "la variabile X non conta".

---

## In R

Il pacchetto è `glmnet`.

```r
library(glmnet)

# glmnet vuole una MATRICE di predittori e un vettore di risposta
x <- model.matrix(target ~ . - 1, data = train)   # crea anche le dummy
y <- train$target

# alpha = 1 -> lasso ; alpha = 0 -> ridge
fit <- glmnet(x, y, family = "binomial", alpha = 1)
plot(fit, xvar = "lambda", label = TRUE)   # i coefficienti che si spengono

# il lambda giusto, in cross-validation
cv <- cv.glmnet(x, y, family = "binomial", alpha = 1)
plot(cv)

cv$lambda.min   # il lambda che sbaglia di meno
cv$lambda.1se   # il piu' severo fra quelli ancora buoni -> modello piu' corto

coef(cv, s = "lambda.1se")   # i punti "." sono le variabili azzerate
```

> [!tip] `lambda.min` o `lambda.1se`?
> `lambda.min` è quello con l'errore più basso. `lambda.1se` è il **più severo fra quelli che restano entro una deviazione standard dal minimo**.
>
> Il secondo dà un modello più piccolo che sbaglia quasi uguale — ed è di solito la scelta migliore. È esattamente la stessa logica della [[Alberi decisionali#La regola a una deviazione standard|regola a una deviazione standard]] nella potatura degli alberi: **a parità di risultato vince il più semplice**.

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| A cosa serve? | a **semplificare** il modello e frenare l'overfitting |
| Come? | una **tassa sui coefficienti grandi** |
| Cos'è **λ**? | quanto è cara la tassa |
| Come scelgo λ? | in **cross-validation** |
| λ = 0? | nessuna tassa, modello normale |
| **Ridge**? | penalità al **quadrato** → rimpicciolisce tutti |
| **Lasso**? | penalità in **valore assoluto** → ne **azzera** alcuni |
| Chi fa selezione? | il **lasso** |
| Cos'è l'**elastic net**? | un misto dei due |
| Devo standardizzare? | **sì** |
| Il lasso dice cosa è importante? | **no**: fra correlate ne tiene una a caso |
| Comando R? | `cv.glmnet(x, y, alpha = 1)` |

## Vedi anche

[[Preprocessing]] · [[Validazione]] · [[PCA]] · [[Modelli lineari]] · [[Regressione logistica]] · [[R]]
