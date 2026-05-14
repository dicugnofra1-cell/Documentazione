# V1 Decisioni Architetturali

## Scopo

Questo documento consolida le decisioni tecniche e architetturali della V1 di Stella.

La V1 non e pensata come semplice ecommerce di mandorle.
E una prima versione operativa della piattaforma, centrata su:

- entrata merce
- lotti
- movimenti
- disponibilita
- vendite
- tracciabilita
- fatture minime per B2B

## Obiettivo V1

Mettere online una versione coerente e realmente usabile del sistema, capace di coprire il flusso principale della filiera:

- ingresso merce
- creazione automatica lotto
- movimento di ingresso
- disponibilita magazzino
- lavorazioni
- vendita B2C o B2B
- scarico
- QR tracciabilita
- fattura solo per B2B

Regola chiave:

`registrazione ingresso = creazione lotto`

Non deve esistere una seconda operazione manuale separata per creare il lotto.

## Flusso operativo V1

### 1. Entrata merce

Quando arriva merce:

- l'operatore registra l'ingresso
- inserisce:
  - fornitore
  - prodotto
  - varieta
  - quantita
  - unita di misura
  - BIO / non BIO
  - documentazione
  - certificazione, se presente
  - note

Il backend:

- salva l'ingresso
- crea automaticamente il lotto
- crea automaticamente il movimento di ingresso
- aggiorna la disponibilita
- restituisce il codice lotto

### 2. Lotto

Il lotto e l'entita centrale del sistema.

Il lotto deve rappresentare:

- identita della merce
- origine
- documentazione
- certificazioni
- movimenti
- disponibilita
- vendite collegate
- QR
- storico operativo

Stati minimi V1:

- Ricevuto
- In lavorazione
- Disponibile
- Confezionato
- Parzialmente venduto
- Esaurito
- Bloccato

### 3. Magazzino e disponibilita

La disponibilita deve seguire questa formula:

`disponibile = fisico - riservato`

Questa logica e la base di:

- magazzino
- vista lotti
- assegnazione FIFO
- ordini
- scarico

### 4. Lavorazioni e confezionamento

Ogni lavorazione deve essere registrata come movimento.

Il prodotto risultante deve restare collegato al lotto di origine.

Il sistema deve mantenere il legame:

`lotto origine -> lavorazione -> prodotto venduto`

### 5. Vendite

#### B2C

Flusso minimo:

- ordine
- pagamento
- spedizione
- scarico
- QR

#### B2B

Flusso minimo:

- vendita
- scelta lotto
- quantita
- scarico
- fattura
- QR se richiesto

Regola V1:

- B2C e B2B condividono lo stesso stock
- B2C e B2B condividono gli stessi lotti
- i clienti si separano solo come anagrafica, non come magazzino

### 6. Assegnazione lotto

La V1 usa una logica:

`FIFO semi-automatico`

Il backend propone:

- lotto piu vecchio disponibile
- stesso prodotto
- stessa tipologia
- stessa condizione BIO / non BIO

L'operatore puo:

- confermare
- cambiare lotto manualmente

### 7. QR e tracciabilita

Il QR non e una funzione isolata.
E uno strumento della tracciabilita.

Il QR deve derivare dal lotto realmente usato nella vendita.

La vista pubblica deve mostrare almeno:

- azienda
- codice lotto
- prodotto
- origine / fornitore
- BIO / non BIO
- certificazione, se presente
- controlli qualita rilevanti
- movimenti o lavorazioni rilevanti
- data vendita o confezionamento

### 8. Fatture

Il modulo Fatture serve solo per il canale B2B.

Per il B2C non serve una fatturazione strutturata nel backoffice V1.

Nel B2C bastano:

- stato pagamento
- riferimento pagamento
- ricevuta / conferma nel dettaglio ordine

## Backoffice V1

La navigazione V1 e:

- Dashboard
- Merce
- Lotti
- Magazzino
- Vendite
- Clienti
- Fatture
- Tracciabilita

### Principi UI

Il backoffice non deve sembrare:

- ERP classico
- dashboard enterprise
- interfaccia tecnica per sviluppatori

Deve sembrare:

- operativo
- semplice
- premium
- leggibile
- usabile tutto il giorno

Ogni schermata deve avere una funzione principale chiara.

## Clienti

### B2C

- creati da ecommerce
- dati essenziali
- molti utenti
- poca gestione manuale

### B2B

- creati o gestiti da backoffice
- pochi ma importanti
- ragione sociale
- P.IVA
- PEC
- SDI da aggiungere
- storico acquisti
- collegamento con fatture

## Stack tecnico V1

### Backend

- C#
- architettura:
  - Domain
  - Application
  - Infrastructure
  - Api

### Frontend

- Angular

### Database

- SQL Server

### Pagamenti

Per il B2C e previsto pagamento online.
Il provider piu naturale da valutare e Stripe.

Per il B2B il pagamento online non e il flusso principale della V1.

## Collegamento frontend / backend

Per la V1:

- niente layer intermedio obbligatorio
- frontend Angular -> backend API

Un BFF potra essere introdotto piu avanti, soprattutto lato ecommerce, se servira separare meglio i canali.

## Pagamenti ecommerce

La conferma pagamento non deve essere decisa dal frontend.

Il flusso corretto e:

- checkout
- provider pagamento
- webhook backend
- verifica firma
- idempotenza
- aggiornamento ordine
- prosecuzione flusso operativo

Per la V1:

- niente code obbligatorie
- webhook affidabile
- stato pagamento registrato dal backend

Le code potranno essere introdotte dopo, se servira disaccoppiare:

- conferma pagamento
- riserva lotto
- scarico
- QR
- notifiche

## Testing e quality gate

Poiche il codice dovra passare un quality gate in DevOps, la V1 va costruita gia in modo testabile.

Strategia:

- ogni blocco implementativo deve essere accompagnato da test
- non rimandare tutti i test alla fine

### Unit test prioritari

- `RegisterGoodsReceipt`
- disponibilita reale
- assegnazione FIFO
- creazione ordine con lotto
- scarico ordine
- aggiornamento tracciabilita

### Integration test prioritari

- ingresso merce -> lotto -> movimento
- ordine -> riserva -> scarico
- lotto reale -> QR / vista pubblica
- vendita B2B -> fattura minima

## Strategia database e ambienti

Scelta attuale:

- sviluppo locale con SQL Server

Direzione consigliata per ambienti reali:

- database cloud
- preferibilmente Azure SQL, se il progetto resta in ecosistema Microsoft

Percorso consigliato:

- locale per sviluppo
- ambiente test / staging
- produzione

## Stato attuale del backend

Gia impostati:

- ingresso merce unificato
- creazione lotto automatica
- movimento di ingresso automatico
- disponibilita reale
- proposta FIFO
- riserva ordine
- scarico da lotto reale
- tracciabilita pubblica piu ricca
- stati e tipi centralizzati meglio nel codice

## Prossimi passi

Ordine consigliato:

1. chiudere bene il dominio lotto / magazzino
2. chiudere il minimo B2B:
   - fatture
   - SDI cliente
3. eseguire un giro completo end-to-end:
   - ingresso
   - lotto
   - vendita
   - scarico
   - QR
4. completare unit test
5. completare integration test
6. preparare rilascio DevOps
