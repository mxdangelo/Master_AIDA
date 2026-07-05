---
date: 2026-07-05
tags: [analytics, strumenti]
status: active
area: analytics
---

# Data Visualization (con matplotlib)

Rendere il dato **leggibile a occhio**: un grafico è un modello visivo, e come ogni modello sceglie cosa mostrare e cosa nascondere. Al Master la base è **matplotlib** in Python ([[Python|pandas]] per i dati, poi si disegna). Repo del corso: [Naviden/basic_python_visualization](https://github.com/Naviden/basic_python_visualization).

> [!info] matplotlib — potente e ingombrante
> Dopo NumPy è **la libreria più grande** dell'ecosistema Python: fa tutto, ma per grafici veloci è spesso **overkill**. È il motore sotto molte altre (pandas `.plot()`, seaborn). Impari matplotlib per **controllo fine**; per l'esplorazione rapida ci sono strati più alti.

## Anatomia del plot

Tre oggetti annidati — saperli distinguere è il 90% del debugging di un grafico:

- **Figure** — la tela (l'intera immagine, può contenere più grafici).
- **Axes** — il singolo grafico dentro la figura (assi, dati, titolo). *Non* è "l'asse".
- **Axis** — gli assi veri e propri (x e y): scala, tick, etichette.

> [!tip] Prima la struttura, poi i dati
> Domanda ricorrente: *come costruisco prima l'impianto del grafico e poi ci verso i valori?* → l'**API a oggetti**. Crei figura e axes vuoti, poi li popoli:
> ```python
> fig, ax = plt.subplots()      # struttura
> ax.plot(x, y)                 # dati, assegnati dopo
> ax.set_title("...")           # rifiniture
> ```
> È l'approccio da preferire su script seri (più esplicito e riusabile) rispetto ai comandi `plt.*` "al volo".

> [!warning] `plt` costruisce un oggetto: l'ordine conta
> Le chiamate `plt.*` **accumulano** stato sulla stessa figura corrente: `title`, `scatter`, `xlim`… si sommano nell'ordine in cui le scrivi. Non è dichiarativo — è una sequenza di modifiche a un oggetto che cresce fino a `show()`/`savefig()`.

## Tipi di grafico — quando usare cosa

| Grafico | Mostra | Uso tipico |
|---|---|---|
| **Line** | andamento continuo | serie temporali |
| **Bar** | confronto tra categorie | vendite per regione |
| **Histogram** | distribuzione di **una** variabile | frequenza dei valori |
| **Scatter** | relazione tra **due** variabili | correlazione, cluster |
| **Box plot** | distribuzione + outlier per gruppo | confronto tra gruppi |
| **Pie** | quote di un totale | (usa con parsimonia: l'occhio confronte male gli angoli) |

## In pratica — lo scatter del case study

Il *case study* bike-sharing (scatter umidità vs temperatura, colori condizionali) propone **di proposito** una versione complessa e inefficiente, per poi mostrare come si fa bene. Le due a confronto valgono più di mille spiegazioni.

> [!fail] Come **non** farlo — uno `scatter` dentro il `for`
> ```python
> for i in range(len(df)):
>     if df.holiday[i] == 0:                       # non festivo
>         alpha, size = 0.3, 28
>         c = 'g' if (df.hum[i] < np.mean(df.hum) and df.atemp[i] > np.mean(df.atemp)) else 'b'
>     else:                                        # festivo
>         c, alpha, size = 'r', 0.8, 40
>     plt.scatter(df.hum[i], df.atemp[i], c=c, alpha=alpha, s=size, edgecolors='k')
> ```
> Funziona, ma chiama `scatter` **una volta per riga**: migliaia di chiamate (lento), verboso, illeggibile. E `np.mean(df.hum)` ricalcolato a ogni giro.

**✅ Come farlo** — le condizioni diventano array con `np.where`, e `scatter` si chiama **una volta sola**:

```python
import numpy as np
mean_hum   = df.hum.mean()
mean_atemp = df.atemp.mean()

green  = (df.holiday == 0) & (df.hum < mean_hum) & (df.atemp > mean_atemp)
colors = np.where(df.holiday == 1, 'r', np.where(green, 'g', 'b'))
alphas = np.where(df.holiday == 1, 0.8, 0.3)
sizes  = np.where(df.holiday == 1, 40, 28)

plt.figure(figsize=(10, 7))
plt.scatter(df.hum, df.atemp, c=colors, alpha=alphas, s=sizes, edgecolors='k')
plt.title('Humidity Vs. Temperature\nHolidays are shown in red', fontsize=16)
plt.xlabel('Humidity'); plt.ylabel('Temperature')
plt.hlines(mean_atemp, 0, 1, 'grey', linestyles='--')   # linee di riferimento (le medie)
plt.vlines(mean_hum,  0, 1, 'grey', linestyles='--')
plt.text(0.08, 0.8, 'Lower humidity, higher temperature\nthan the average.')
plt.xlim(0, 1); plt.ylim(0, 1)
plt.savefig('scatter.png', dpi=150, bbox_inches='tight')   # NON screenshot dell'IDE
plt.show()
```

> [!tip] Dettagli che tornano
> - **Salva con `savefig()`**, non con lo screenshot dell'editor: risoluzione piena, riproducibile, versionabile.
> - **Gli assi non devono partire da (0,0)**: `xlim`/`ylim` scelgono la finestra. Zoomare sul range utile è legittimo — falsare i confronti no.
> - `range(a, b)` arriva a **b−1**: l'estremo destro è **escluso** (come lo slicing Python).
> - **Vettorializza** con `np.where` invece dei cicli: più veloce e più leggibile (stesso principio di [[Python#Serie temporali e colonne condizionali (dal corso)|pandas]]).

## Oltre matplotlib — il panorama

| Libreria | Forza | Quando |
|---|---|---|
| **matplotlib** | controllo totale | grafici statici, pubblicazione, base di tutto |
| **seaborn** | statistica, bello di default | distribuzioni, correlazioni, poche righe |
| **plotly** | interattivo (zoom, hover) | esplorazione, web |
| **bokeh** | interattivo + selezione | dashboard in-browser |
| **Dash** (Plotly) | app/dashboard | prodotti data, non solo grafici |

> [!question] Codice o tool no-code?
> **Come scelgo lo strumento di visualizzazione?** Dipende da: **ambiente aziendale** e quindi dagli **stakeholder** (cosa già usano, cosa sanno leggere) + preferenza personale, **facilità**, **flessibilità** richiesta e **velocità** di realizzazione. Matplotlib/Python dà flessibilità massima e costo di scrittura alto; un tool BI come [[Power BI]] dà velocità e condivisione al prezzo di meno controllo. → tabella di scelta nel [[Prontuario#Quale tool per visualizzare?|Prontuario]].

## Principi del buon grafico

> [!important] Ogni elemento deve avere un senso
> - **Niente decorazione muta**: colori ed etichette devono portare informazione **univoca**, non abbellire. Un colore che non codifica una variabile è rumore.
> - La domanda guida è sempre quella della [[BI Architecture|BI]]: *qual è la domanda a cui il grafico risponde?* Da lì si scelgono variabili, tipo e assi.
> - La visualizzazione è l'**ultimo** anello: un grafico bello su un dato sporco mente meglio. → prima il [[Data Quality#Da tenere in tasca|data profiling]].

## Vedi anche

[[Python]] · [[Superstore — profittabilità]] · [[Power BI]] · [[BI Architecture]] · [[Machine Learning]]
