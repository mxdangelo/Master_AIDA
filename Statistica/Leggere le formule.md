---
date: 2026-09-19
tags: [statistica]
status: active
image: "[[assets/covers/statistica.svg]]"
area: statistica
---

# Leggere le formule

**In una riga:** i simboli che ti bloccano la lettura, sciolti uno per uno.

> [!important] La cosa da sapere prima di tutto
> Quasi tutte le formule di statistica dicono la stessa identica cosa:
>
> **"fai questo conto per ogni persona, poi mettili insieme".**
>
> I simboli strani servono a scrivere quella frase in modo compatto. Una volta che li riconosci, la formula si legge come una ricetta.

---

## Σ — la sommatoria

**Sigma maiuscola. Vuol dire: somma tutto.**

```
  n
  Σ  xᵢ
 i=1
```

Si legge: **"somma le x, da i uguale 1 fino a n"**. Cioè: prendi il primo valore, il secondo, il terzo… fino all'ultimo, e sommali.

| pezzo | cosa dice |
|---|---|
| `Σ` | somma |
| `i = 1` sotto | da dove parti: dal primo |
| `n` sopra | dove arrivi: fino all'ultimo |
| `xᵢ` | **cosa** sommi |

> [!example] Sciolta per intero
> Quattro persone, altezze 170, 165, 180, 175.
>
> ```
>  4
>  Σ  xᵢ   =   x₁ + x₂ + x₃ + x₄   =   170 + 165 + 180 + 175   =   690
> i=1
> ```
>
> Tutto qui. Il simbolo esiste solo perché scrivere `x₁ + x₂ + … + x₂₃₅₇` è scomodo.

E infatti la media, che sai già calcolare, scritta per bene è:

```
        1    n
  x̄  =  ─    Σ  xᵢ
        n   i=1
```

*"Somma tutti i valori e dividi per quanti sono."* La stessa cosa che facevi a mente.

> [!tip] Quando gli estremi spariscono
> Spesso trovi solo `Σᵢ xᵢ`, o addirittura `Σ xᵢ`, senza i numeretti sopra e sotto.
>
> Non è un'altra operazione: vuol dire **"somma su tutti"**, e gli estremi si omettono perché sono ovvi. Non cercare significati nascosti.

---

## ∏ — la produttoria

**Pi greco maiuscola. Identica alla sommatoria, ma moltiplica invece di sommare.**

```
  n
  ∏  xᵢ   =   x₁ × x₂ × x₃ × … × xₙ
 i=1
```

Si legge **"produttoria"**, o semplicemente *"il prodotto di tutti gli x"*.

> [!example] Con gli stessi quattro numeri
> ```
> ∏ xᵢ  =  170 × 165 × 180 × 175  =  883.575.000
> ```

Stessa struttura di Σ, stessa grammatica. **Cambia solo il segno dell'operazione.**

### Dove la incontri davvero

Nella **verosimiglianza**, ed è l'unico posto dove la userai spesso:

```
        n
  L  =  ∏  pᵢ^yᵢ · (1 − pᵢ)^(1 − yᵢ)
       i=1
```

Sembra terribile. Tradotta, dice: *"per ogni persona calcola quanto ci ha preso il modello, poi moltiplica tutti i risultati fra loro"* (→ [[Regressione logistica#La verosimiglianza L, calcolata a mano]]).

> [!info] Perché lì si moltiplica e non si somma
> Perché sono **probabilità di cose che succedono insieme**.
>
> Due dadi che fanno entrambi 6: `1/6 × 1/6`. Non `1/6 + 1/6`, che darebbe un terzo — più probabile di un singolo 6, il che è assurdo.
>
> Probabilità congiunte di eventi indipendenti **si moltiplicano** (→ [[Probabilità e distribuzioni#Le poche regole che servono]]).

### Il ponte fra le due

Ed ecco perché nei libri la produttoria sparisce quasi subito, sostituita da una sommatoria:

```
ln( a × b × c )  =  ln a  +  ln b  +  ln c
```

**Il logaritmo trasforma i prodotti in somme.** Quindi:

```
ln ( ∏ qualcosa )   =   Σ ln(qualcosa)
```

Questo è tutto il motivo per cui esiste la **log-verosimiglianza**. Non è un concetto nuovo: è la stessa quantità, resa maneggevole. La produttoria di cinquecento numeri minori di 1 sprofonda a zero e il computer la perde; la somma dei loro logaritmi no.

---

## Pedici, apici e cappelli

| scrittura | nome | cosa dice |
|---|---|---|
| `xᵢ` | **pedice** | la x **della persona i-esima**. La `i` è un segnaposto: "una qualunque" |
| `x₁`, `x₂` | pedice numerico | la prima, la seconda |
| `x²` | **apice / esponente** | x **al quadrato** |
| `x̄` | **barra** | la **media** di quelle x. Si legge "x segnato" |
| `μ̂` | **cappello** (*hat*) | una **stima**, non il valore vero |

> [!warning] Apice ed esponente si confondono
> In `pᵢ^yᵢ`, il `y` **in alto** è un esponente: elevi `p` a `y`. Il `ᵢ` **in basso** dice solo di quale persona stai parlando.
>
> Sotto = chi. Sopra = quanto lo elevi.

> [!important] Il cappello è il simbolo più informativo di tutti
> `μ` è la media **vera** della popolazione: non la conoscerai mai.
> `μ̂` è la tua **stima**, calcolata sui dati che hai.
>
> Ogni volta che vedi un cappello, il testo ti sta dicendo *"attenzione, questo è un numero che abbiamo tirato fuori noi, e potrebbe sbagliare"* (→ [[Inferenza#La stima è un numero ballerino]]).

---

## Le lettere greche

> [!tip] La regola che vale quasi sempre
> **Greca = il valore vero, ignoto, della popolazione.**
> **Latina = quello che hai calcolato tu sul campione.**
>
> `μ` la media vera, `x̄` la tua. `σ` la deviazione vera, `s` la tua. `β` il coefficiente vero, `b` quello stimato.
>
> Da qui il cappello: `β̂` significa "la mia stima di quel coefficiente vero", ed è un altro modo di scrivere `b`.

| simbolo | nome | dove lo incontri |
|---|---|---|
| **μ** | mu | la media della popolazione |
| **σ** | sigma | la deviazione standard. `σ²` è la varianza |
| **β** | beta | i coefficienti di un modello ([[Modelli lineari]], [[Regressione logistica]]) |
| **α** | alfa | la soglia di significatività, di solito 0,05 ([[Test statistici]]) |
| **λ** | lambda | il numero medio di eventi nella Poisson |
| **π** | pi greco | una proporzione nella popolazione. Non il 3,14 |
| **ρ** | rho | la correlazione vera. Quella stimata è `r` |
| **χ²** | chi quadro | il test sulle tabelle di frequenza |
| **ε** | epsilon | l'errore, il residuo di un modello |
| **θ** | theta | un parametro generico, quando non si vuole specificare quale |
| **Δ** | delta | una differenza, una variazione |

> [!info] π ha due vite
> In geometria è 3,14159. In statistica è **una proporzione** — la quota di successi nella popolazione.
>
> Non c'entrano niente l'uno con l'altro: gli alfabeti sono corti e i simboli si riciclano. Vale la stessa cosa per la `e`, che a volte è il numero 2,718 e a volte l'errore.

---

## Gli altri simboli

| simbolo | si legge | esempio |
|---|---|---|
| `\|` | **"dato che"**, "sapendo che" | `P(Y\|X)` → probabilità di Y **sapendo** X |
| `~` | **"si distribuisce come"** | `X ~ N(0,1)` → X segue una Normale |
| `~` (in R) | **"spiegato da"** | `y ~ x` → y spiegato da x |
| `≈` | circa uguale | |
| `∈` | appartiene a | `x ∈ A` → x sta dentro A |
| `±` | più o meno | `174 ± 2` → la fascia da 172 a 176 |
| `∞` | infinito | |
| `n` | quante osservazioni hai | il campione |
| `N` | quante ne ha la popolazione | |
| `!` | **fattoriale** | `4! = 4×3×2×1 = 24` |

> [!warning] La tilde `~` vuol dire due cose diverse
> In una formula di teoria: **"si distribuisce come"**.
>
> In [[R]]: **"spiegato da"**, dentro `lm()` e `glm()`.
>
> Si capisce dal contesto: se a destra c'è il nome di una distribuzione è la prima, se ci sono nomi di colonne è la seconda.

---

## Due formule tradotte

### La varianza

```
        1    n
 s²  =  ─    Σ  (xᵢ − x̄)²
       n−1  i=1
```

Letta da destra verso sinistra, un pezzo alla volta:

1. `(xᵢ − x̄)` → per ogni persona, **quanto si discosta dalla media**
2. `( … )²` → **eleva al quadrato**, così i segni non si annullano
3. `Σ` → **somma** tutti questi scarti al quadrato
4. `1/(n−1)` → **dividi**, per farne una media

*"La media degli scarti dalla media, al quadrato."* Che è esattamente la definizione a parole in [[Variabilità#Varianza]].

### Il logit

```
              pᵢ
 logit(pᵢ) = ln ───────  =  β₀ + β₁xᵢ
             1 − pᵢ
```

1. `pᵢ / (1 − pᵢ)` → gli **odds**: la probabilità divisa il suo complemento
2. `ln( … )` → il **logaritmo** degli odds, cioè il logit
3. `= β₀ + β₁xᵢ` → **è una retta**

*"Il logaritmo degli odds è una retta"* → [[Regressione logistica#Le tre scale: probabilità, odds, logit]].

---

## Come si affronta una formula sconosciuta

> [!tip] Quattro passi, in quest'ordine
> 1. **Cerca Σ o ∏.** Se ci sono, la formula fa qualcosa "per ogni persona" e poi mette insieme. Chiediti solo *cosa* fa per ciascuna
> 2. **Guarda dentro il simbolo**, ignorando tutto il resto. Quella è l'operazione vera
> 3. **Controlla i cappelli.** Dicono cosa è stimato e cosa è vero
> 4. **Traducila in italiano.** Se riesci a dirla a voce, l'hai capita. Se non riesci, il buco è in uno dei simboli, non nel concetto
>
> Nessuna formula di questo corso dice qualcosa che non si possa dire a parole in una frase.

---

## Da tenere in tasca

| simbolo | significa |
|---|---|
| **Σ** | **somma** tutti |
| **∏** | **moltiplica** tutti (produttoria) |
| `ln(∏)` = `Σ ln` | il logaritmo trasforma **prodotti in somme** |
| `xᵢ` | la x **della persona i** |
| `x̄` | la **media** delle x |
| `μ̂` | una **stima**, non il valore vero |
| greca / latina | valore **vero** / valore **calcolato da te** |
| `μ` `σ` `σ²` | media, deviazione standard, varianza |
| `β` `α` `ε` | coefficienti, soglia di significatività, errore |
| `\|` | **"dato che"** |
| `~` | "si distribuisce come" · in R "spiegato da" |
| `n` | quante osservazioni |

## Vedi anche

[[Percorso di studio]] · [[Valori medi]] · [[Variabilità]] · [[Probabilità e distribuzioni]] · [[Regressione logistica]] · [[Inferenza]]
