---
date: 2026-07-07
tags: [fondamentali]
status: active
image: "[[assets/covers/fondamentali.svg]]"
area: fondamentali
---

# Processi — vita, morte e zombie

Un **processo** è un programma in esecuzione: il sistema operativo gli assegna un **PID** e una voce nella *process table*. Nei sistemi Unix-like ogni processo nasce da un **padre** e, quando termina, lascia un **exit status** che il padre deve leggere.

## Zombie process

Un processo **zombie** (o *defunct*) ha **finito di eseguire ma resta nella process table**: il figlio ha chiamato `exit()`, il padre non ha ancora chiamato `wait()` per ritirarne l'exit status. Finché non lo fa, il sistema conserva la voce — e il PID resta occupato.

- Si riconosce con `ps`: **`Z`** nella colonna STAT.
- `kill` **non ha effetto**: non c'è più niente da terminare.
- La pulizia si chiama **reaping**: il padre chiama `wait()`/`waitpid()`, tipicamente quando riceve il segnale **SIGCHLD**.
- Se il padre muore prima, lo zombie viene adottato da **`init`**, che lo raccoglie periodicamente.

## Zombie ≠ orfano

| | **Zombie** | **Orfano** |
|---|---|---|
| Stato | terminato, in attesa di reaping | ancora in esecuzione |
| Cosa manca | il padre non ha fatto `wait()` | il padre è morto |
| Destino | resta finché non viene raccolto | adottato da `init`, vive normalmente |

> [!warning] Perché tanti zombie sono un problema
> Quasi zero memoria, ma ogni zombie **occupa un PID** (risorsa finita) e può trattenere file descriptor. Un processo che genera figli senza mai raccoglierli può esaurire i PID del sistema.

> [!tip] In Python
> `subprocess.run()` e `Process.join()` (multiprocessing) raccolgono i figli per te. Gli zombie spuntano quando lanci figli "a mano" (`Popen`) e non chiami mai `wait()`.

## Vedi anche

[[Python]] · [[Spark]] (processi distribuiti: driver ed executor)

Fonte: [Zombie process](https://en.wikipedia.org/wiki/Zombie_process) (Wikipedia)
