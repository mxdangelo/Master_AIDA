---
date: 2026-09-19
tags: [data-mining]
status: active
image: "[[assets/covers/data-mining.svg]]"
area: data-mining
---

# Scegliere la soglia

**In una riga:** il modello dà una probabilità, tu devi dare una risposta — e il confine fra sì e no lo decidi tu.

Un classificatore non dice "questo cliente è insolvente". Dice **0,37**. Per agire serve una regola:

```
se probabilità > soglia  →  dico SÌ
altrimenti               →  dico NO
```

Il default è **0,5**, che equivale a *"vince la classe più probabile"* (→ [[Classificatore di Bayes#La regola di decisione]]).

> [!warning] 0,5 non è una legge di natura
> È solo il punto in cui le due probabilità si pareggiano. Presuppone due cose che quasi mai sono vere: che le classi siano **bilanciate**, e che sbagliare in un verso costi **quanto** sbagliare nell'altro.
>
> Cambiare la soglia non cambia il modello di una virgola. **Sposta solo dove tagli**, e con quello sposti il tipo di errore che accetti.

---

## Il problema degli eventi rari

> [!example] Il modello che sembra perfetto e non serve a niente
> Le frodi sono l'**1%** delle transazioni. Costruisci un modello, la soglia è 0,5, e l'accuratezza è del **99%**.
>
> Poi guardi cosa fa davvero: dice **"non è frode" a tutto**. Non ne ha trovata una.
>
> Ha ragione il 99% delle volte perché il 99% delle transazioni è pulito. **L'accuratezza, su classi sbilanciate, è una misura inutile.**

Con un prior basso quasi nessuna probabilità stimata supera 0,5, e la classe rara sparisce. La cura è abbassare la soglia — ma di quanto, e in base a cosa?

---

## I due errori non sono lo stesso errore

Si parte dalla [[Valutare un classificatore|matrice di confusione]]:

| | **è davvero sì** | **è davvero no** |
|---|---|---|
| **prevedo sì** | **VP** — preso giusto | **FP** — falso allarme |
| **prevedo no** | **FN** — mancato | **VN** — scartato giusto |

E dalle due misure che contano, che guardano le due **colonne**:

| misura | formula | risponde a |
|---|---|---|
| **sensibilità** (*recall*) | `VP / (VP + FN)` | **di tutti i malati, quanti ne ho trovati?** |
| **specificità** | `VN / (VN + FP)` | **di tutti i sani, quanti ne ho lasciati in pace?** |

> [!important] Si oppongono, sempre
> **Abbassi la soglia** → dici "sì" più spesso → trovi più malati (**sensibilità su**) ma spaventi più sani (**specificità giù**).
>
> **Alzi la soglia** → dici "sì" più di rado → meno falsi allarmi (**specificità su**) ma ti sfugge più gente (**sensibilità giù**).
>
> Non esiste una soglia che le massimizzi entrambe. **Devi scegliere quale errore ti fa meno male.**

> [!tip] Due casi opposti, per fissarlo
> - **screening oncologico** → vuoi **sensibilità alta**. Un falso allarme costa un esame in più; un malato mancato costa una vita. Soglia bassa
> - **filtro antispam** → vuoi **specificità alta**. Un po' di spam in posta è fastidio; un'email di lavoro persa è un disastro. Soglia alta

---

## La curva ROC

Se la soglia cambia tutto, tanto vale **provarle tutte** e guardare cosa succede.

La **curva ROC** fa esattamente questo: per ogni soglia da 0 a 1 segna un punto, con la sensibilità in verticale e i falsi allarmi in orizzontale.

```
sensibilità
    1 ┤        ╭────────  ← modello buono: sale subito
      │      ╭─╯
      │    ╭─╯      ╱     ← la diagonale = tirare a caso
      │  ╭─╯     ╱
      │╭─╯    ╱
    0 ┼────────────────
      0    falsi allarmi   1
```

| dove passa la curva | che modello è |
|---|---|
| sulla **diagonale** | inutile: come lanciare una moneta |
| **sopra** la diagonale, gonfia verso l'alto a sinistra | buono |
| nell'**angolo in alto a sinistra** | perfetto: prende tutti i sì senza un falso allarme |

> [!info] Perché la curva è utile: non dipende dalla soglia
> Ogni punto **è** una soglia. La curva quindi descrive il **modello nel suo complesso**, indipendentemente da dove deciderai di tagliare.
>
> Serve a due cose diverse: **confrontare modelli** (quale curva sta più in alto) e poi **scegliere la soglia** su quello vincente (quale punto della curva ti va bene).

### AUC

**AUC** = *area under the curve*, l'area sotto la curva. Riassume tutto in un numero.

| AUC | significa |
|---|---|
| **0,5** | inutile, la diagonale |
| 0,7 – 0,8 | accettabile |
| 0,8 – 0,9 | buono |
| **1,0** | perfetto |

> [!tip] Cosa vuol dire davvero l'AUC
> È la probabilità che, pescando **a caso un sì e a caso un no**, il modello dia al sì la probabilità più alta.
>
> AUC = 0,85 significa: nell'85% delle coppie il modello mette le due persone nell'ordine giusto. **È una misura di ordinamento**, non di soglia — ed è per questo che non risente dello sbilanciamento delle classi.

---

## Scegliere la soglia sul serio

### Tre criteri

| criterio | come si sceglie | quando |
|---|---|---|
| **naive** | la decide chi usa il modello, per esperienza: *"dammi le probabilità, poi decido io"* | c'è una prassi aziendale consolidata |
| **statistico** | guardi sul validation come cambiano accuratezza, sensibilità, precisione, falsi positivi e falsi negativi al variare della soglia, e scegli quella che ti serve | non conosci i costi, ma sai quale errore pesa di più |
| **dei costi** | la soglia esce dalla matrice di profitto | conosci quanto vale ogni giusto e ogni errore |

> [!example] Il criterio statistico, con i numeri
> Un'azienda vuole trovare **i clienti che compreranno**. Perderne uno costa più che contattare un non interessato. Quindi conta la **sensibilità**.
>
> Sul validation prova varie soglie e sceglie **0,10**: molto bassa, dice "compra" spesso. La sensibilità arriva al **93,7%**: quasi tutti i compratori veri vengono presi.
>
> Se il 93,7% basta, il modello con soglia 0,10 passa allo score dei casi nuovi.

Ogni dataset va studiato a sé: in una frode pesa di più il falso negativo, in una campagna costosa il falso positivo.

> [!question] La scelta da difendere
> **Cosa scegli:** il criterio, e quindi il valore della soglia.
>
> **Che problema risolve:** di **business**. Decide quale errore accetti: più falsi allarmi o più mancati. Dove la cerchi, invece, è statistica: sul validation, mai sul test.
>
> **Cosa ti costa:** ogni criterio ha la sua ipotesi. Tenere **0,5** assume classi bilanciate ed errori di pari costo. Anche lo **Youden** assume che i due errori pesino uguale. Il criterio **dei costi** assume che quei costi siano stimati bene.
>
> **All'esame:** *"Scrivere costa 0,68 $, perdere un donatore 14,86 $. Il falso negativo pesa 22 volte il falso positivo, quindi soglia 0,044. L'ho cercata in cross-validation, non sul test."* → [[Il metodo]]

### Se i due errori pesano uguale

Si prende il punto della curva più vicino all'angolo perfetto, o quello che massimizza `sensibilità + specificità − 1` (**indice di Youden**). È una scelta puramente geometrica.

### Se i due errori costano diverso

Qui si smette di fare statistica e si fanno i conti.

> [!example] La banca, con i numeri
> - concedere un prestito a un cliente **buono** → guadagni **100 €**
> - concederlo a uno **cattivo** → perdi **500 €**
> - rifiutarlo → zero, in entrambi i casi
>
> Conviene concedere quando il guadagno atteso è positivo:
>
> ```
> p × 100  −  (1 − p) × 500  >  0
> ```
>
> Risolta, dà `p > 500 / 600 = 0,833`.
>
> **La soglia è 0,83, non 0,5.** E non è un'opinione: esce dal rapporto fra i costi. Se il cliente non è buono all'83%, il prestito non conviene.

La regola generale:

```
                    costo del falso positivo
soglia  =  ───────────────────────────────────────────────
           costo del falso positivo + costo del falso negativo
```

### La formula con i profitti

La formula sopra considera **solo i costi degli errori**: i giusti valgono zero. Quando anche i giusti hanno un valore, si parte dalla regola del [[Valutare un classificatore#Il profitto atteso di una persona|profitto atteso]]: dici "sì" quando `EP(sì) > EP(no)`.

Risolta per `p`, dà la **soglia ottimale bayesiana**:

```
                          1
soglia  =  ─────────────────────────────
                  valore(VP) − valore(FN)
           1  +  ─────────────────────────
                  valore(VN) − valore(FP)
```

Il numeratore della frazione piccola è **quanto guadagni** a dire "sì" a un sì vero invece di "no". Il denominatore è **quanto guadagni** a dire "no" a un no vero invece di "sì".

> [!example] La lettera per la raccolta fondi
> Scrivere a chi risponde vale **+14,86 $**. Scrivere a chi non risponde costa **−0,68 $**. Non scrivere vale **0**.
>
> ```
> soglia = 1 / (1 + (14,86 − 0) / (0 − (−0,68)))  =  1 / (1 + 21,85)  ≈  0,044
> ```
>
> Conviene scrivere a chiunque abbia **più del 4,4%** di probabilità di rispondere. Un francobollo costa poco, una donazione vale tanto.

> [!example] La banca, rifatta con la formula
> Ogni 5 $ prestati, 1 $ di profitto. Prestare a un buon cliente: **+1**. Prestare a un cattivo: **−5**. Rifiutare: **0**.
>
> ```
> soglia = 1 / (1 + (1 − 0) / (0 − (−5)))  =  1 / (1 + 0,2)  =  0,833
> ```
>
> Stesso risultato del conto fatto sopra. Due regole equivalenti: *"presta se EP(presto) > 0"* e *"presta se p > 0,833"*.

> [!warning] La soglia si sceglie fuori dal test set
> Trovare la soglia migliore **sul test set** e poi riportare quanto funziona **su quello stesso test set** è barare: hai usato quei dati per scegliere.
>
> La soglia va cercata sul validation set, o meglio sulle probabilità ottenute in **cross-validation** dal training. Così il test resta davvero indipendente (→ [[Validazione#Dividere i dati]]).

---

## Il prior sbagliato

C'è un secondo motivo per cui le probabilità possono essere fuori bersaglio, e non si cura con la soglia.

> [!warning] Quando il training è bilanciato apposta
> Per far imparare meglio il modello, spesso si costruisce un dataset **bilanciato**: si tengono tutti gli insolventi e solo una parte dei buoni, arrivando a un 30% di insolventi.
>
> Nella realtà gli insolventi sono il **5%**.
>
> Il modello impara a essere **sei volte più sospettoso** di quanto dovrebbe, e tutte le probabilità che produce sono **gonfiate**. Non è un problema di soglia: sono proprio i numeri a essere sbagliati.
>
> Vanno **corretti con il prior vero** prima di qualsiasi decisione (→ [[Classificatore di Bayes#Problema 2 — i dati di training sono bilanciati, la realtà no]]). In [[SAS]] si scrive `pevent=` e `priorevent=`.

### Come si corregge

Si distinguono due prior:

- **prior del campione** (`π`): la quota di ogni classe **nel dataset** su cui hai addestrato
- **prior veri** (`π'`): la quota di ogni classe **nella popolazione**

Ogni probabilità stimata si moltiplica per il rapporto `π' / π` della sua classe. Poi si divide per la somma, così le due probabilità tornano a fare 1:

```
                     p₁ × π'₁/π₁
p₁ corretta  =  ─────────────────────────────
                p₀ × π'₀/π₀  +  p₁ × π'₁/π₁
```

> [!example] Con i numeri
> Training: **30%** di insolventi. Popolazione: **5%**. Il modello dà a un cliente `p = 0,5` di essere insolvente.
>
> - insolvente: `0,5 × 0,05/0,30` = 0,083
> - buono: `0,5 × 0,95/0,70` = 0,679
>
> Probabilità corretta: `0,083 / (0,083 + 0,679)` ≈ **0,11**.
>
> Il modello diceva "testa o croce". Con il prior vero, il cliente è insolvente **una volta su nove**.

La correzione **spinge le probabilità verso la classe più comune** nella popolazione. Cambiano la matrice di confusione, i profitti attesi e quasi tutte le misure. **Sensibilità e specificità restano uguali** (→ [[Valutare un classificatore#Da cosa dipendono]]).

---

## Lo score dei casi nuovi

L'ultimo passo: usare il modello su persone di cui **non conosci** la risposta (→ [[Validazione#I quattro passi del data mining]]). Si chiama **score**.

La regola è: **si riapplica la stessa scelta fatta sul validation**.

| come hai scelto | come classifichi i nuovi |
|---|---|
| criterio naive o statistico | la stessa soglia: `se p > soglia allora sì` |
| criterio dei costi | per ogni persona calcoli gli **EP** e scegli la decisione con l'EP più alto |

> [!example] Due regole di score
> - *"se la probabilità di sopravvivere supera 0,85, il paziente è classificato come sopravvissuto"*
> - *"concedi il prestito se l'EP del prestito è positivo"*. Puoi anche essere più esigente: *"solo se l'EP supera 1,30 $"*

---

## In R

```r
library(pROC)

# le probabilita' stimate, non le classi
p <- predict(m, newdata = validation, type = "prob")[, "si"]

roc_obj <- roc(validation$target, p)
plot(roc_obj)
auc(roc_obj)

# la soglia che massimizza sensibilita' + specificita'
coords(roc_obj, "best", ret = c("threshold", "sensitivity", "specificity"))

# la soglia dettata dai costi
soglia <- 500 / (500 + 100)

previsti <- factor(ifelse(p > soglia, "si", "no"), levels = c("no", "si"))
caret::confusionMatrix(previsti, validation$target, positive = "si")
```

> [!tip] `confusionMatrix` e l'argomento `positive`
> Sensibilità e specificità **si scambiano** a seconda di quale classe consideri l'evento.
>
> `caret` di default prende come positiva la **prima** in ordine alfabetico, che quasi mai è quella che ti interessa. Dichiaralo sempre esplicitamente con `positive = "si"`, altrimenti leggi i numeri al contrario.

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| Perché non tenere 0,5? | presuppone classi **bilanciate** ed errori che costano **uguale** |
| Cambiare soglia cambia il modello? | **no**. Sposta solo dove tagli |
| L'accuratezza va bene? | **no** su classi sbilanciate: il 99% può voler dire "non trova niente" |
| **Sensibilità**? | dei sì veri, quanti ne ho **presi** |
| **Specificità**? | dei no veri, quanti ne ho **lasciati stare** |
| Si possono massimizzare entrambe? | **no**, si oppongono |
| Cos'è la **ROC**? | la curva di tutte le soglie possibili |
| Cos'è l'**AUC**? | l'area sotto. **0,5 = inutile, 1 = perfetto** |
| Cosa misura l'AUC? | la probabilità di **ordinare giusto** una coppia |
| Soglia dai costi? | `costo FP / (costo FP + costo FN)` |
| Dove la cerco? | su **validation** o in cross-validation, **mai sul test** |
| Training bilanciato? | correggi le probabilità con il **prior vero** |
| I tre criteri? | **naive**, **statistico**, **dei costi** |
| Soglia con i profitti? | `1 / (1 + (VP − FN) / (VN − FP))`, coi valori della matrice |
| Score dei nuovi casi? | la **stessa** soglia, o l'EP più alto |

## Vedi anche

[[Validazione]] · [[Valutare un classificatore]] · [[Confrontare i modelli]] · [[Classificatore di Bayes]] · [[Regressione logistica]] · [[Machine Learning]] · [[SAS]] · [[R]]
