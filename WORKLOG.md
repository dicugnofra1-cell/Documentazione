# WORKLOG

## Scopo

Questo file tiene traccia sintetica delle decisioni, dei cambi di direzione e dei passaggi importanti del progetto Stella.

---

## 2026-05-12

### Stato

- Separato il ragionamento tra repository di codice e repository di documentazione.
- Definito che la documentazione servira come ponte tra Codex, ChatGPT e continuita progettuale.
- Chiarito che il codice reale restera versionato separatamente.
- Definiti i flussi operativi principali della piattaforma Stella a livello documentale.
- Analizzato il prototipo attuale del backoffice e definiti gap, schermate mancanti e moduli necessari per trasformarlo in una piattaforma operativa reale.
- Rafforzata la distinzione operativa tra B2C e B2B nei flussi interni.
- Formalizzato il modulo Fatture come modulo autonomo del backoffice.
- Definita una mappa piu concreta della piattaforma operativa backoffice con schermate minime e priorita UX.

### Decisioni

- Il backend, il monitor e la libreria condivisa non devono essere duplicati qui.
- Questa cartella deve raccogliere:
  - contesto
  - roadmap
  - worklog
  - prototipi
  - documentazione tecnica e funzionale
- Il backoffice non va pensato come semplice supporto all'ecommerce, ma come piattaforma operativa autonoma.
- Il modulo Fatture deve essere trattato come parte strutturale del prodotto e non come dettaglio accessorio degli ordini.

### Prossimi passi

- creare struttura iniziale della documentazione
- importare i documenti gia esistenti
- organizzare le aree `docs/`, `prototypes/`, `references/`
- dettagliare moduli e schermate definitive del backoffice a partire dai flussi
- consolidare la mappa definitiva del backoffice per ruolo, sezione e priorita di sviluppo
- tradurre la mappa documentale in una revisione concreta del prototipo backoffice
- chiarire V1 e V2 dei moduli interni prima di toccare il codice applicativo

---

## Regola pratica

Ogni volta che succede una di queste cose, aggiornare il worklog:

- decisione importante
- cambio di priorita
- nuova milestone
- chiusura di una fase
- chiarimento tra stato reale e stato previsto
