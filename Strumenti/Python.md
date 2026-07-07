---
date: 2026-05-11
tags: [strumenti]
status: active
image: "[[assets/covers/strumenti.svg]]"
area: strumenti
---

# Python — pandas per il dato

Il coltellino svizzero del dato in Python: **pandas** (da NumPy) lavora **in memoria** su tabelle (`DataFrame`) e serie (`Series`). È la base del corso (Colab) e il ponte verso [[Spark|PySpark]] quando i dati non stanno più in RAM. Riferimento: *Python for Data Analysis* — Wes McKinney (creatore di pandas).

> [!info] Riconciliato col corso (*15-Pandas*) + repo del prof
> Il corso Python completo (fondamentali → pandas) è nella repo del prof: [Naviden/Python_Introduction](https://github.com/Naviden/Python_Introduction) — lezioni 01→15, esercizi *by topic*, project-based. Qui sotto un **ripasso del linguaggio** e la **sintesi pandas**; per il percorso completo, la repo.

## Fondamentali del linguaggio (ripasso)

Le basi che tornano di continuo lavorando col dato — un concetto per blocco, con l'esempio accanto.

**Variabili e tipi** — Python è a tipi dinamici: il tipo sta nel *valore*, non nella variabile.
```python
n = 42            # int
x = 3.14          # float
s = "ciao"        # str
ok = True         # bool
niente = None     # assenza di valore (≈ NULL in SQL)
```

**f-string** — comporre testo con valori dentro: `f"{store}: {tot:.2f} €"` (il `:.2f` formatta con 2 decimali).

**Le 4 collezioni** — quale usare dipende da cosa devi farci:

| Collezione | Sintassi | Proprietà | Usala per |
|---|---|---|---|
| `list` | `[1, 2, 3]` | ordinata, modificabile | sequenze, righe |
| `tuple` | `(1, 2)` | ordinata, immutabile | coppie fisse |
| `dict` | `{"k": v}` | chiave → valore | record, lookup (≈ documento [[MongoDB]]) |
| `set` | `{1, 2, 3}` | senza duplicati | dedup, test di appartenenza |

**Controllo di flusso** — l'indentazione (4 spazi) *è* la sintassi, non estetica: delimita il blocco.
```python
for r in righe:
    if r["prezzo"] > 10:
        print(r["nome"])
```

**Funzioni** — un blocco riusabile con un solo compito ([[Principi#^srp|single responsibility]]):
```python
def netto(prezzo, iva=0.22):      # iva ha un valore di default
    """Prezzo senza IVA."""       # docstring: dice a cosa serve
    return prezzo / (1 + iva)
```

**Errori** — leggi il *traceback* dal basso: l'ultima riga dice il tipo di errore e dove. Per gestirne uno atteso:
```python
try:
    df = pd.read_csv(path)
except FileNotFoundError:
    print(f"manca il file: {path}")
```

**Import e ambienti** — gli import stanno in cima al file. Le librerie si installano con `pip install pandas`, meglio dentro un **virtual environment** (`python -m venv .venv`) così ogni progetto ha le sue versioni. Su Colab è già tutto pronto.

## Stile: PEP 8

Lo standard di stile del codice Python. Il motivo: *"il codice si legge molto più spesso di quanto si scrive"*.

- **Indentazione**: 4 spazi · **righe**: max 79 caratteri.
- **Nomi**: `snake_case` per funzioni e variabili, `CapWords` per classi, `MAIUSCOLO` per costanti. Mai `l`, `O`, `I` da soli (si confondono con `1` e `0`).
- **Import** in cima, raggruppati: standard library → terze parti → moduli tuoi.
- **Confronti**: `x is None` (non `== None`) · `if lista:` (non `if len(lista) > 0`).
- Spazi attorno agli operatori binari (`a + b`), non dentro le parentesi.

> [!tip] La clausola di flessibilità
> *"A foolish consistency is the hobgoblin of little minds"*: se una regola rende il codice **meno** leggibile o rompe la coerenza col codice esistente, ignorala. Lo stile serve la lettura, non viceversa. → [PEP 8](https://peps.python.org/pep-0008/)

## List comprehension

Lo zucchero sintattico più usato in Python — costruire una collezione in una riga (anche dentro pandas, es. selezionare colonne per nome):
```python
[x * 2 for x in nums]                         # mappa
[x for x in nums if x > 0]                    # filtro
[c for c in df.columns if "order" in c]       # colonne il cui nome contiene "order"
{k: v for k, v in d.items() if v}             # dict comprehension
```

## Caricare e ispezionare

```python
import pandas as pd
df = pd.read_csv("file.csv")           # anche read_json, read_parquet, read_excel
df.head()        # prime righe          df.shape       # (righe, colonne)
df.info()        # tipi + non-null      df.describe()  # statistiche numeriche
df["categoria"].value_counts()         # conteggio per valore
```

## Selezionare

```python
df["col"]                               # una colonna (Series)
df[["a", "b"]]                          # più colonne (DataFrame)
df.loc[df["eta"] > 18, ["nome", "eta"]] # righe (condizione) + colonne, per ETICHETTA
df.iloc[0:5, 0:2]                       # per POSIZIONE
```

## Pulire ([[Data Quality]] in pratica)

```python
df.isna().sum()                         # quanti NA per colonna
df = df.dropna(subset=["id"])           # togli righe con NA nel campo chiave
df["prezzo"] = df["prezzo"].fillna(0).astype(float)
df = df.drop_duplicates(subset=["id"])
df["citta"] = df["citta"].str.strip().str.lower()   # normalizza stringhe
```

## Aggregare e combinare

```python
df.groupby("store")["vendite"].sum()
df.groupby(["store", "data"]).agg(tot=("vendite", "sum"), n=("id", "count")).reset_index()
df.pivot_table(index="store", columns="mese", values="vendite", aggfunc="sum")

pd.merge(a, b, on="id", how="left")     # JOIN (how: inner/left/right/outer)
pd.concat([df1, df2], ignore_index=True)
```

## Tanti file insieme

```python
import glob
files = glob.glob("data/**/*.csv", recursive=True)   # '.' = cartella corrente
df = pd.concat((pd.read_csv(f) for f in files), ignore_index=True)
```

## Serie temporali e colonne condizionali (dal corso)

```python
import numpy as np
ts.resample("M", on="date").mean()                      # aggrega per mese
close_px.rolling(90)["AAPL"].mean()                     # media mobile 90 giorni (finance)
df["status"] = np.where(df["ordini"] < 110, "High", "Low")   # colonna da condizione
df["cat"].value_counts(normalize=True)                  # frequenze relative (%)
```

> [!warning] Cosa NON si può / gotcha
> - **pandas è in-memory**: scala fino alla **RAM**. Oltre → [[Spark|PySpark]] (stesso ragionamento, distribuito).
> - Per **assegnare** usa `.loc`: `df.loc[m, "c"] = x`. Il *chained indexing* `df[m]["c"] = x` non funziona (SettingWithCopyWarning).
> - **Vettorializza**: evita `for`/`df.iterrows()` → opera su colonne intere (più veloce di ordini di grandezza).

> [!tip] Come fare X
> - "quanti per categoria" → `df["cat"].value_counts()`
> - "colonna calcolata" → `df["tot"] = df["q"] * df["prezzo"]`
> - "filtra e ordina" → `df[df.x > 10].sort_values("x", ascending=False)`
> - "porta in [[Spark]]" → `spark.createDataFrame(df)` · "torna a pandas" → `sdf.toPandas()`

> [!tip] Scrivi codice ri-eseguibile
> Punta all'**idempotenza**: rilanciare lo stesso codice non deve rompere nulla. Se rieseguendo una cella Python "ignora" ciò che ha già fatto (o protesta), è segno che il codice è scritto coi piedi — non che Python fa i capricci. → [[ETL#Idempotenza|idempotenza]].

## Vedi anche

[[Spark]] · [[Data Quality]] · [[Data Ingestion]] · [[Data Visualization]]
