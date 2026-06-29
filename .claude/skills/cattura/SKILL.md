---
name: cattura
description: Processa inbox.md — smista e formatta le catture grezze nelle note di studio. Si invoca con /cattura o dicendo "processa l'inbox".
---

# Cattura

Trasforma le righe grezze di `inbox.md` in contenuto distribuito e formattato nelle note.

## Cosa fa

1. Leggi la sezione *"Da processare"* di `inbox.md`.
2. Per ogni riga o blocco:
   - **URL** → recupera il contenuto e integralo nella nota di tema pertinente (o proponi una nota nuova).
   - **appunto / idea** → smistalo nella nota giusta (o creane una), formattando in modo coerente col resto: titoli, callout (`[!tip]`, `[!warning]`, `[!info]`, `[!quote]`), wiki-link, diagrammi Mermaid dove aiutano.
3. Aggiorna i **wiki-link** e, se serve, il `Prontuario`.
4. **Svuota** dall'inbox le righe processate.
5. Mostra cosa è andato dove.

## Principi

- Dove ci sono riflessioni personali, **preserva le parole** di chi scrive; formatta, non riscrivere.
- Una nota per topic; collega con `[[...]]`.
- Tieni le note navigabili: ogni nota chiude con un footer **Vedi anche**.
- Verifica che i diagrammi Mermaid e i blocchi di codice restino bilanciati.
