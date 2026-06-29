---
tags: [meta]
---

# Dashboard — navigazione

Vista **auto-generata** di tutte le note di studio, raggruppate per area. Si aggiorna da sola quando aggiungi, rinomini o ri-categorizzi una nota — basta che la nota abbia la property `area` nel frontmatter.

> [!info] Richiede Obsidian 1.9+
> Le **Bases** sono native ma recenti: questa vista rende solo dentro Obsidian aggiornato, non su GitHub né nello ZIP. Per la mappa valida ovunque resta il [[Prontuario#🗺️ Mappa delle note (per area)|Prontuario]].

```base
filters:
  and:
    - '!file.hasTag("meta")'
views:
  - type: table
    name: Note per area
    groupBy:
      property: area
      direction: ASC
    order:
      - area
      - file.name
      - status
      - date
    sort:
      - property: area
        direction: ASC
      - property: file.name
        direction: ASC

```

> [!tip] Varianti
> - Il filtro `!file.hasTag("meta")` tiene fuori le pagine dell'ossatura (Prontuario, Principi, README…); le note di studio hanno tutte una `area`.
> - Per una vista a **schede** invece che tabella, cambia `type: table` → `type: cards`.
> - Per i soli lavori in corso, aggiungi a `filters.and`: `- 'status == "active"'`.

## Principi

Transclusi da [[Principi]] — una sola fonte, citata qui con `![[Principi#^id]]`:

![[Principi#^gigo]]
![[Principi#^modelli]]
![[Principi#^attante]]
![[Principi#^move-compute]]
![[Principi#^idempotenza]]
![[Principi#^domanda]]
