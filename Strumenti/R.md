---
date: 2026-09-19
tags: [strumenti]
status: active
image: "[[assets/covers/strumenti.svg]]"
area: strumenti
---

# R

**In una riga:** un linguaggio di programmazione fatto **apposta** per la statistica.

[[Python]] è un linguaggio generalista a cui sono state aggiunte le librerie statistiche. R è nato dentro la statistica: cose come "fai una regressione" o "disegna una densità" sono **comandi di base**, non pacchetti da installare.

---

## Installazione

Servono **due programmi separati**, in quest'ordine:

1. **R** — il motore, da [r-project.org](https://www.r-project.org). Fa i conti. Da solo ha un'interfaccia spartana
2. **RStudio** — l'ambiente di lavoro, da [posit.co](https://posit.co/download/rstudio-desktop/). È la finestra in cui lavori davvero

> [!warning] Prima R, poi RStudio
> RStudio **non contiene** R: lo cerca sul computer. Installato al contrario, si lamenta di non trovare nessuna installazione di R.

### I quattro pannelli di RStudio

```
┌─────────────────────┬─────────────────────┐
│  SCRIPT             │  ENVIRONMENT        │
│  il tuo codice,     │  gli oggetti che    │
│  si salva           │  hai in memoria     │
├─────────────────────┼─────────────────────┤
│  CONSOLE            │  PLOTS / HELP       │
│  dove escono i      │  i grafici e la     │
│  risultati          │  documentazione     │
└─────────────────────┴─────────────────────┘
```

> [!tip] Scrivi nello script, non nella console
> La console dimentica tutto appena chiudi. Lo script è un file che si salva e si riesegue.
>
> Metti il cursore su una riga dello script e premi **Ctrl+Invio**: quella riga viene eseguita nella console. È così che si lavora — una riga alla volta, guardando cosa esce.

---

## Le basi

### Assegnare

```r
a <- 5           # la freccia <- e' l'assegnazione tipica di R
b <- "prova"     # le virgolette servono per il testo
a = 5            # funziona anche l'uguale, ma nessuno lo usa
```

La freccia `<-` si scrive con `Alt` + `-` (c'è la scorciatoia apposta).

### Vettori

Il vettore è **una fila di valori**, ed è l'oggetto base di R. Si costruisce con `c()`, dove `c` sta per *concatenate*.

```r
eta <- c(25, 31, 44, 19)
eta                       # stampa la fila intera
length(eta)               # quanti sono → 4
eta[2]                    # il SECONDO elemento → 31
```

> [!warning] In R si conta da 1
> `eta[1]` è il primo elemento. Se arrivi da [[Python]], dove il primo è `[0]`, è la fonte di errori numero uno.

Scorciatoie per costruire vettori:

```r
1:20                      # tutti i numeri da 1 a 20
seq(1, 2, by = 0.1)       # 1.0, 1.1, 1.2 … 2.0
rep(25, times = 20)       # venti volte il numero 25
```

### Filtrare

```r
eta > 30                  # TRUE/FALSE per ogni elemento
eta[eta > 30]             # tiene SOLO quelli sopra 30
which(eta > 30)           # le POSIZIONI di quelli sopra 30
```

La logica è: dentro le quadre ci metti una condizione, e resta solo chi la soddisfa.

| operatore | significa |
|---|---|
| `==` | uguale (**doppio**! `=` singolo assegna) |
| `!=` | diverso |
| `>` `<` `>=` `<=` | maggiore, minore… |
| `&` | **e** |
| `\|` | **o** |
| `!` | non |

```r
ifelse(eta >= 40, "Over40", "Under40")   # se… allora… altrimenti
```

### Il data.frame

È **la tabella**: righe e colonne, come un foglio Excel. È l'oggetto su cui si lavora sempre.

```r
df <- data.frame(eta = eta, fuma = c(1, 0, 0, 1))

df$eta            # la colonna eta — il $ e' il modo normale
df[1, ]           # la prima RIGA, tutte le colonne
df[, "eta"]       # tutte le righe, la colonna eta
df[df$eta > 30, ] # solo le righe con eta sopra 30
```

> [!tip] La virgola dentro le quadre separa righe e colonne
> `df[righe, colonne]`. Se lasci vuoto uno dei due posti, prende tutto.
>
> `df[1, ]` = prima riga, tutte le colonne. `df[, 1]` = tutte le righe, prima colonna.

### Guardare i dati appena arrivati

```r
head(dati)        # le prime 6 righe
str(dati)         # che tipo e' ogni colonna
summary(dati)     # min, max, media, quartili di tutte
dim(dati)         # quante righe e quante colonne
table(dati$fumo)  # conta quante volte esce ogni valore
```

`summary()` e `str()` sono i due comandi da lanciare **sempre** su un dataset nuovo, prima di qualsiasi altra cosa.

---

## Caricare i dati

```r
getwd()                              # in che cartella sono?
setwd("C:/Users/utente/dati")        # spostati li'

dati <- read.csv("studenti.csv")     # un CSV
load("studenti.Rdata")               # un file gia' in formato R
```

> [!warning] Le barre nei percorsi
> Windows scrive `C:\Users\...` con la barra rovesciata, ma in R quella ha un altro significato.
>
> Scrivi `C:/Users/...` con la barra normale, oppure `C:\\Users\\...` raddoppiata.

> [!tip] Il modo che non si rompe mai
> In RStudio: **Session → Set Working Directory → To Source File Location**. Imposta la cartella dello script che hai aperto, senza scriverla a mano.

---

## I pacchetti

R di base fa già molto. Il resto sta nei **pacchetti**, che si installano una volta e si caricano a ogni sessione.

```r
install.packages("psych")    # UNA VOLTA SOLA, scarica da internet
library(psych)               # OGNI VOLTA che apri R
```

> [!warning] L'errore più comune all'inizio
> `Error: could not find function "..."` quasi sempre significa che hai dimenticato la riga `library()`.
>
> Installare non basta: installare è comprare il libro, `library()` è aprirlo.

Quelli che girano nel corso:

| pacchetto | a cosa serve |
|---|---|
| `psych` | `pairs.panels()`: tutte le variabili incrociate in un colpo |
| `lmtest` | `lrtest()`: confronto fra modelli (→ [[Regressione logistica#Il test LRT: serve davvero questa variabile?\|il test LRT]]) |
| `car` | `vif()`: scova le variabili che dicono la stessa cosa |
| `caret` | la cassetta degli attrezzi del machine learning |
| `ggplot2` | i grafici belli |

---

## La formula con la tilde

È **la cosa più caratteristica di R**, e una volta capita vale per ogni modello.

```r
y ~ x
```

La tilde `~` si legge **"spiegato da"**. Quindi `fumo ~ peso` è *"il fumo spiegato dal peso"*.

| scrittura | vuol dire |
|---|---|
| `y ~ x` | y spiegato da x |
| `y ~ x1 + x2` | y spiegato da x1 **e** x2 |
| `y ~ .` | y spiegato da **tutte** le altre colonne |
| `y ~ x1 * x2` | x1, x2 **e** la loro interazione |
| `y ~ . - eta` | tutte **tranne** eta |

Il punto `.` significa "tutto il resto" ed è comodissimo per una prima prova.

---

## I modelli

Due comandi soli, e cambiano per una cosa sola: **che tipo di risposta vuoi**.

```r
# y e' un NUMERO (prezzo, reddito, altezza)
m <- lm(prezzo ~ metri + piano, data = case)

# y e' SI'/NO (fuma, insolvente, spam)
m <- glm(fumo ~ peso + eta, data = dati, family = "binomial")

summary(m)     # il risultato, in tutti e due i casi
```

Cosa guardare in `summary()` sta in [[Regressione logistica#Leggere il risultato in R]] e in [[Modelli lineari#In R]].

Dopo aver stimato un modello:

```r
coef(m)                    # i coefficienti
confint(m)                 # gli intervalli di confidenza
exp(coef(m))               # gli odds ratio (solo per la logistica)
predict(m, type = "response")   # le previsioni
plot(m)                    # i grafici diagnostici sui residui
AIC(m1, m2, m3)            # confronta piu' modelli: vince il piu' basso
```

---

## Grafici in due righe

```r
plot(dati$peso, dati$altezza)    # nuvola di punti
hist(dati$eta)                   # istogramma
boxplot(eta ~ genere, data = dati)  # confronto fra gruppi
barplot(table(dati$fumo))        # barre su una variabile categoriale
```

Per i grafici da mostrare ad altri si usa `ggplot2` (→ [[Data Visualization]]).

---

## Quando qualcosa non va

| messaggio | cosa vuol dire |
|---|---|
| `could not find function "x"` | manca `library(...)` |
| `object 'x' not found` | l'oggetto non esiste: refuso, o non hai eseguito la riga che lo crea |
| `cannot open file` | percorso sbagliato o `setwd()` in un'altra cartella |
| `argument of length 0` | stai lavorando su un oggetto vuoto |
| il `+` nella console | **manca una parentesi o una virgoletta**. Premi `Esc` e ricontrolla |

L'ultima riga è la più fastidiosa all'inizio: se la console mostra `+` invece di `>`, R sta aspettando che tu finisca il comando.

```r
help(rep)      # la documentazione di una funzione
?rep           # uguale, piu' corto
```

---

## Da tenere in tasca

| serve | comando |
|---|---|
| assegnare | `x <- 5` |
| creare un vettore | `c(1, 2, 3)` |
| prime righe | `head(dati)` |
| riassunto | `summary(dati)` |
| tipi delle colonne | `str(dati)` |
| conteggi | `table(dati$col)` |
| leggere un CSV | `read.csv("file.csv")` |
| caricare un pacchetto | `library(nome)` |
| modello su numero | `lm(y ~ x, data = d)` |
| modello su sì/no | `glm(y ~ x, data = d, family = "binomial")` |
| previsioni | `predict(m, type = "response")` |
| eseguire una riga | **Ctrl+Invio** |
| aiuto | `?nomefunzione` |

## Vedi anche

[[Regressione logistica]] · [[Modelli lineari]] · [[Inferenza]] · [[SAS]] · [[Python]]
