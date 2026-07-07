---
tags: [meta]
---

# Il metodo: inbox → l'AI distribuisce

Come sono fatti questi appunti — e come puoi tenerli vivi anche tu.

## L'idea

Non scrivo le note "in bella" a mano. **Catturo grezzo** in [`inbox`](inbox.md) (un pensiero, un URL, un appunto al volo), poi chiedo a un **assistente AI** di leggerlo e:

- **smistarlo** nella nota giusta (o crearne una nuova),
- **formattarlo** in modo coerente col resto (titoli, callout, wiki-link, diagrammi Mermaid),
- tenere tutto **navigabile** (link tra note, [`Prontuario`](Prontuario.md) aggiornato).

L'inbox si **svuota** man mano che il materiale viene distribuito.

## Come farlo

1. Apri questa cartella come **vault [Obsidian](https://obsidian.md)**.
2. Butta in `inbox.md` quello che vuoi ricordare.
3. Apri un assistente AI con accesso ai file — es. **Claude Code** lanciato in questa cartella, oppure un plugin AI di Obsidian (Copilot, Text Generator) — e digli: *"processa l'inbox: smista e formatta nelle note giuste"*.
4. Rivedi e rifinisci.

> [!tip] Non serve una skill
> Basta la **chat** con un LLM che legge e scrive i file. Per **Claude Code** c'è anche la scorciatoia `/cattura` (in `.claude/skills/`), ma è opzionale: funziona con qualunque modello.

## Stile delle note

Le spiegazioni devono essere **inclusive**: leggibili anche da chi ha difficoltà di lettura, cognitive o ADHD. Meglio un rigo in più che un passaggio dato per scontato. In pratica:

- **frasi brevi**, un'idea per frase; i passaggi espliciti, non sottintesi;
- ogni concetto astratto ha un **esempio concreto** accanto;
- **grassetto** sui termini chiave, **callout** per avvisi e domande-chiave;
- niente muri di testo: liste, tabelle, diagrammi Mermaid;
- il gergo si introduce con la spiegazione la prima volta, poi si usa.

## Punto di partenza

[`Prontuario`](Prontuario.md) — la mappa operativa degli appunti (cosa usi quando, snippet, lookup).
