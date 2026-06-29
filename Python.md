---
date: 2026-05-11
tags: [strumenti]
status: active
---

# Python — pandas per il dato

Il coltellino svizzero del dato in Python: **pandas** (da NumPy) lavora **in memoria** su tabelle (`DataFrame`) e serie (`Series`). È la base del corso (Colab) e il ponte verso [[Spark|PySpark]] quando i dati non stanno più in RAM. Riferimento: *Python for Data Analysis* — Wes McKinney (creatore di pandas).

> [!info] Riconciliato col corso (*15-Pandas*) + repo del prof
> Il corso Python completo (fondamentali → pandas) è nella repo del prof: [Naviden/Python_Introduction](https://github.com/Naviden/Python_Introduction) — lezioni 01→15, esercizi *by topic*, project-based. Qui la **sintesi pandas**; per le basi del linguaggio, la repo.

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

[[Spark]] · [[Data Quality]] · [[Data Ingestion]]
