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

## 2026-05-13

### Stato

- Analizzato il repository backend reale per verificare l'allineamento tra dominio esistente e V1 del backoffice.
- Confermata una buona base su:
  - lotti
  - movimenti
  - ordini
  - riserve stock
  - fornitori
  - certificazioni
  - qualita
  - tracciabilita pubblica
- Chiarito che la V1 richiede soprattutto flussi applicativi orchestrati, piu che nuovi moduli astratti.

### Decisioni

- La V1 del backoffice deve essere centrata su **entrata merce, lotti e movimenti**, non solo sugli ordini ecommerce.
- Il lotto resta l'entita centrale del sistema.
- La disponibilita V1 deve essere letta come:
  - stock reale da movimenti
  - meno stock riservato
- Fatture ingrosso vanno tenute leggere in V1:
  - documento minimo
  - niente modulo fiscale avanzato

### Output

- Creato il documento:
  - [ALLINEAMENTO_V1_BACKOFFICE_DOMINIO.md](/C:/Users/ricca/Documents/Playground/Documentazione/docs/backend/ALLINEAMENTO_V1_BACKOFFICE_DOMINIO.md)

### Prossimi passi

- tradurre l'allineamento V1 in backlog implementativo backend
- decidere le modifiche minime al dominio per `Entrata merce`
- allineare il prototipo backoffice ai campi reali della V1

---

## 2026-05-14

### Stato

- Consolidato il modello operativo V1 attorno alla tracciabilita reale della merce.
- Chiarito che il lotto nasce automaticamente dalla registrazione ingresso.
- Chiarito che ogni vendita deve restare collegata al lotto realmente usato.
- Riallineata la struttura del backoffice V1 in modo piu semplice e operativo.

### Decisioni

- `Merce` e il punto di ingresso operativo del sistema.
- `Lotti` resta l'entita centrale del prodotto.
- `B2C` e `B2B` condividono lo stesso flusso stock e lotti.
- `Clienti` si separa solo in anagrafica:
  - `B2C`
  - `B2B`
- `Fatture` serve solo per il canale `B2B`.
- `Tracciabilita` sostituisce una logica troppo centrata sul solo `QR`.
- La navigazione V1 fissata e:
  - Dashboard
  - Merce
  - Lotti
  - Magazzino
  - Vendite
  - Clienti
  - Fatture
  - Tracciabilita

### Output

- aggiornato:
  - [ALLINEAMENTO_V1_BACKOFFICE_DOMINIO.md](/C:/Users/ricca/Documents/Playground/Documentazione/docs/backend/ALLINEAMENTO_V1_BACKOFFICE_DOMINIO.md)
  - [REVISIONE_V1_MINIMALE_BACKOFFICE.md](/C:/Users/ricca/Documents/Playground/Documentazione/docs/backoffice/REVISIONE_V1_MINIMALE_BACKOFFICE.md)
  - [docs/backend/README.md](/C:/Users/ricca/Documents/Playground/Documentazione/docs/backend/README.md)
  - [docs/backoffice/README.md](/C:/Users/ricca/Documents/Playground/Documentazione/docs/backoffice/README.md)
  - [V1_DECISIONI_ARCHITETTURALI.md](/C:/Users/ricca/Documents/Playground/Documentazione/docs/backend/V1_DECISIONI_ARCHITETTURALI.md)

### Prossimi passi

- completare la pulizia finale del prototipo backoffice
- congelare la V1 UI
- passare al backlog tecnico backend partendo da:
  - `RegisterGoodsReceipt`
  - disponibilita reale
  - assegnazione lotto FIFO
  - collegamento ordine-lotto-QR

---

## Regola pratica

Ogni volta che succede una di queste cose, aggiornare il worklog:

- decisione importante
- cambio di priorita
- nuova milestone
- chiusura di una fase
- chiarimento tra stato reale e stato previsto
