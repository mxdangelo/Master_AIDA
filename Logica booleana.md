---
date: 2026-06-26
tags: [fondamentali]
status: active
---

# Logica booleana

Il linguaggio per formalizzare i **criteri di selezione** — le operazioni condizionali, dove la procedura cambia secondo i valori dei dati ("sconto del 15% a chi compra ≥3 vasche *o* spende >5000"). È la base della clausola `WHERE` di [[SQL]].

Un'**espressione booleana** prende valori in input e restituisce un risultato in `{True, False}`. Il criterio di decisione si descrive con una **tabella di verità**.

## Operatori e tabelle di verità

`0 = False`, `1 = True`.

| a | b | a AND b | a OR b | a XOR b |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 1 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 1 | 1 | 0 |

| a | NOT a |
|---|---|
| 0 | 1 |
| 1 | 0 |

- **OR** (inclusivo) — vero se almeno uno dei due è vero.
- **XOR** (esclusivo) — vero se **uno e uno solo** è vero.

## L'ambiguità del linguaggio naturale

*Spy story*: l'agente "ordina un caffè con panna **o** una cioccolata con latte". Quell'"o" è `OR` o `XOR`? Il testo non lo dice — è ambiguo. Scelta (arbitraria) l'interpretazione inclusiva:

```
agente = (caffè AND panna) OR (cioccolata AND latte)
```

Mr. Z, che ordina *entrambi*, risulta l'agente solo se l'"o" è inclusivo. **Lezione:** prima di tradurre un requisito di business in una condizione, disambigua la lingua — una formulazione più saggia ("*o* l'uno *o* l'altro") avrebbe evitato il dubbio.

## Precedenza

`AND` ha precedenza su `OR`. Parallelo aritmetico:

| Booleano | Aritmetica |
|---|---|
| `AND` | × (moltiplicazione) |
| `OR` | + (somma) |
| `NOT` | − (segno meno) |

Quindi `a OR b AND c` ≡ `a + b × c`: si calcola prima `b AND c`. Le **parentesi** cambiano l'ordine e migliorano comunque la leggibilità.

## Leggi di De Morgan

Controintuitive ma vere (verificabili con la tabella di verità):

```
NOT (A AND B) = (NOT A) OR  (NOT B)
NOT (A OR  B) = (NOT A) AND (NOT B)
```

Negando un'espressione si **scambia** AND con OR. Errore frequentissimo: lasciare lo stesso operatore dentro la negazione.

## Vedi anche

[[SQL]] · [[Relazioni]]
