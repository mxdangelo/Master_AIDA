---
date: 2026-09-19
tags: [statistica]
status: active
image: "[[assets/covers/statistica.svg]]"
area: statistica
---

# Inferenza

**In una riga:** guardo **un pezzetto** della realtà e provo a capire **com'è fatta tutta**.

*Inferire* vuol dire **dedurre**. Parti da un'informazione parziale e arrivi a una conclusione generale.

> [!info] Cosa serve sapere prima
> Questa nota dà per noti media e varianza ([[Valori medi]], [[Variabilità]]) e la curva a campana ([[Probabilità e distribuzioni]]). Se uno dei tre ti suona nuovo, parti da lì.

> [!example] Il pentolone di minestra
> Devi capire se la minestra è salata al punto giusto. Non te la mangi tutta: **assaggi un cucchiaio**.
>
> Il pentolone è la **popolazione**. Il cucchiaio è il **campione**. Decidere com'è tutta la minestra da un cucchiaio solo: quella è l'**inferenza**.
>
> E il trucco della minestra vale anche in statistica: prima di assaggiare, **mescola**. Se assaggi in superficie senza girare, il cucchiaio non rappresenta il pentolone.

---

## Popolazione e campione

| | cos'è | esempio |
|---|---|---|
| **Popolazione** | **tutti** quelli che ti interessano | tutti gli italiani |
| **Campione** | quelli che riesci davvero a misurare | 1.000 italiani intervistati |
| **Parametro** | il numero vero della popolazione. **Non lo saprai mai** | l'altezza media di tutti gli italiani |
| **Stima** | il numero che calcoli sul campione | l'altezza media dei 1.000 intervistati |

> [!important] Il punto di tutto
> Il numero vero della popolazione è **ignoto e resta ignoto**. Nessuno misurerà mai l'altezza di sessanta milioni di persone.
>
> L'inferenza non serve a scoprire quel numero. Serve a dire: *"sta molto probabilmente fra 174 e 176 cm"*. Una **fascia**, e quanto sei sicuro che ci sia dentro.

> [!tip] La mossa che rende tutto possibile
> Sbagliare è inevitabile: stai guardando un pezzetto e parlando del tutto.
>
> Allora la statistica rinuncia a non sbagliare e **si accontenta di controllare di quanto sbaglia**. Quel "di quanto" si chiama **margine d'errore**.
>
> **Se sai quanto sbagli, sai quante possibilità hai di indovinare.** Tutto il resto di questa nota — errore standard, intervalli di confidenza, dimensione del campione — è un modo di tenere quell'errore sotto controllo.

### Come si scrivono

Gli statistici usano lettere greche per i numeri veri, e ci mettono un **cappellino** sopra per dire "questa è la mia stima".

| simbolo | si legge | cos'è |
|---|---|---|
| `μ` | mu | la **media vera** della popolazione |
| `σ²` | sigma quadro | la **varianza vera** |
| `σ` | sigma | la **deviazione standard** vera |
| `μ̂` | mu con cappello | la **stima** della media, fatta sul campione |
| `x̄` | x segnato | la media del campione (stessa cosa di `μ̂`) |
| `n` | | quanti elementi ha il campione |

Il cappellino `^` significa sempre e solo: **"questo non è il vero valore, è quello che ho calcolato io"**.

---

## La curva a campana

La **distribuzione normale** — detta anche **gaussiana**, dal matematico Gauss — è quella forma a campana che hai già visto mille volte.

```mermaid
flowchart LR
    A["pochi<br/>molto bassi"] --> B["TANTI<br/>nella media"] --> C["pochi<br/>molto alti"]
```

Dice una cosa che conosci già per esperienza: **la maggior parte delle persone sta nel mezzo, e gli estremi sono rari.**

Pensa all'altezza. Un sacco di gente fra 165 e 180 cm. Pochissimi sotto il metro e mezzo, pochissimi sopra i due metri. Disegnando quante persone ci sono per ogni altezza viene fuori una campana.

> [!info] Perché è dappertutto
> Una montagna di fenomeni si comporta così: altezza, peso, errori di misura, voti a un esame, tempo di percorrenza.
>
> Succede quando il risultato dipende da **tante piccole cause indipendenti** che si sommano. L'altezza dipende da decine di geni e da come sei cresciuto: qualcuno ha quasi tutti i fattori "alto", qualcuno quasi tutti "basso", ma la maggioranza ne ha un misto e finisce nel mezzo.

### I due numeri che la descrivono

Una campana è definita da **due soli parametri**:

- **`μ` (media)** — **dove sta il centro**. Sposta la campana a destra o a sinistra senza cambiarne la forma.
- **`σ²` (varianza)** — **quanto è larga**. Dice quanto le persone sono diverse fra loro.

> [!tip] Varianza alta = previsioni difficili
> Due classi con la **stessa media** di 6 all'esame:
>
> - **Classe A** — voti fra 5,5 e 6,5. Varianza bassa. Se pesco uno a caso, indovino quasi sicuramente.
> - **Classe B** — metà prende 3, metà prende 9. Media sempre 6, ma varianza altissima. Se pesco uno a caso non ho idea di cosa esca.
>
> **Più varianza c'è, più è difficile prevedere.** La media da sola non basta mai a raccontare un fenomeno.

> [!info] Varianza o deviazione standard?
> Sono la stessa informazione in due formati.
> - la **varianza** `σ²` si calcola con i quadrati, quindi è in un'unità strana (centimetri **al quadrato**)
> - la **deviazione standard** `σ` è la sua radice quadrata, e torna in centimetri normali
>
> Nei discorsi si usa `σ`, perché si legge: "in media le persone si discostano di 7 cm dalla media".

---

## La stima è un numero ballerino

Qui c'è l'idea più importante di tutta l'inferenza, e all'inizio suona strana.

> **La tua stima non è un numero fisso. È un numero che cambia a seconda del campione che ti è capitato.**

> [!example] Provaci mentalmente
> Vuoi l'altezza media degli italiani. Peschi **100 persone a caso** e trovi **173,2 cm**.
>
> Ricominci da capo: peschi **altre 100 persone** e trovi **175,8 cm**.
>
> E ancora: **174,1**. Poi **172,9**. Poi **176,0**.
>
> Stessa popolazione, stesso metodo. **Risultati diversi ogni volta**, perché ogni volta ti capitano persone diverse.

Questo si chiama **variabile aleatoria** — *aleatorio* vuol dire "che dipende dal caso", dal latino *alea*, il dado.

La tua stima è una variabile aleatoria. Non perché tu abbia sbagliato i conti, ma perché il campione te l'ha dato il caso.

> [!important] E allora come faccio a fidarmi?
> Non puoi sapere se il tuo campione è di quelli fortunati. Puoi però sapere **quanto sono sparpagliati i risultati possibili**.
>
> Se tutti i campioni immaginabili danno risultati fra 172 e 176, allora il tuo — qualunque sia — non può essere lontanissimo dal vero.
>
> Se invece danno risultati fra 150 e 200, il tuo numero non vale niente.
>
> **Meno i risultati possibili sono sparpagliati, più puoi fidarti di quello che hai in mano.**

### L'errore standard

Lo sparpagliamento delle stime possibili ha un nome: **errore standard**. E si calcola così:

```
errore standard = σ / √n
```

Leggila guardando dove stanno le lettere:

- **`σ` sta sopra** → più il fenomeno è vario, più le stime ballano. Non è colpa tua, è il fenomeno
- **`n` sta sotto** → più persone intervisti, **meno** le stime ballano

> [!tip] La regola della radice, in pratica
> `n` sta sotto una **radice quadrata**, e questo ha una conseguenza concreta: per **dimezzare** l'errore devi **quadruplicare** il campione.
>
> | intervistati | errore standard |
> |---|---|
> | 100 | 1,0 |
> | 400 | 0,5 |
> | 1.600 | 0,25 |
> | 6.400 | 0,125 |
>
> Ecco perché i sondaggi seri si fermano attorno a mille persone: raddoppiare la precisione costa **quattro volte** tanto. A un certo punto non conviene più.

---

## Intervallo di confidenza

Adesso tutti i pezzi si incastrano.

Non consegni un numero secco. Consegni una **fascia**, più una percentuale che dice quanto ci credi:

> *"L'altezza media degli italiani è **174,3 cm**, con un intervallo di confidenza al 95% da **173,1** a **175,5**."*

La formula è più semplice dell'aria che si dà:

```
stima  ±  circa 2 errori standard
```

Quel "2" viene dalla campana: sotto una gaussiana, **il 95% dei casi cade entro due deviazioni standard dal centro**. È una proprietà della forma, e vale sempre.

> [!warning] Cosa significa davvero "95%"
> **Non** significa "c'è il 95% di probabilità che il valore vero sia lì dentro".
>
> Significa: *se ripetessi il sondaggio mille volte e ogni volta calcolassi la fascia, **950 di quelle fasce conterrebbero il valore vero**.*
>
> Il valore vero sta fermo dov'è. Sono **le fasce** che cambiano a ogni campione. Tu ne hai calcolata una, e non sai se è fra le 950 fortunate o fra le 50 sfortunate.

### Cosa allarga e cosa stringe la fascia

| se… | la fascia |
|---|---|
| aumenti il campione `n` | si **stringe** ✓ |
| il fenomeno è molto vario (`σ` grande) | si **allarga** |
| vuoi più sicurezza (99% invece di 95%) | si **allarga** |

L'ultima riga ha una morale: **precisione e sicurezza si oppongono**. Se vuoi essere certissimo devi dire una cosa più vaga. "Fra 0 e 3 metri" è vero al 100% e non serve a niente.

---

## Il teorema del limite centrale

Obiezione legittima: *e se il mio fenomeno non è una campana?*

Un sacco di cose non lo sono. Gli stipendi no — tanta gente in basso, pochissimi ricchissimi che allungano la coda a destra.

> [!important] La buona notizia
> Non importa.
>
> Anche se il fenomeno di partenza ha una forma qualunque, **la media campionaria diventa comunque una campana**, purché il campione sia abbastanza grande.
>
> Questo risultato si chiama **teorema del limite centrale**, ed è il motivo per cui tutto quanto sopra continua a funzionare quasi sempre.

Detto in modo pratico: gli **stipendi** degli italiani non disegnano una campana. Ma se prendi tante volte 500 italiani e ogni volta calcoli lo **stipendio medio**, quelle medie sì.

Quanto è "abbastanza grande"? La regola spannometrica è **n ≥ 30**. Più il fenomeno è storto, più ne servono.

---

## Da tenere in tasca

| domanda | risposta rapida |
|---|---|
| Cos'è l'inferenza? | capire **tutta** la popolazione da **un** campione |
| Perché non misuro tutti? | costa troppo, e spesso è impossibile |
| Cos'è un parametro? | il numero **vero**, che non conoscerai mai |
| Cosa fa il cappellino `μ̂`? | segnala che è una **stima**, non il vero valore |
| I due numeri della campana? | **media** (dov'è il centro) e **varianza** (quanto è larga) |
| Perché la stima "balla"? | perché il campione te l'ha dato il caso → **variabile aleatoria** |
| Come riduco l'errore? | **aumenti n**. Ma per dimezzarlo devi quadruplicarlo |
| Cos'è l'intervallo di confidenza? | la fascia dentro cui sta ragionevolmente il valore vero |
| E se i dati non sono normali? | con `n` grande la **media** lo diventa lo stesso (TLC) |

## Vedi anche

[[Probabilità e distribuzioni]] · [[Valori medi]] · [[Variabilità]] · [[Test statistici]] · [[Modelli lineari]] · [[Regressione logistica]] · [[R]]
