# Flussi Operativi della Piattaforma Stella

## Scopo

Questo documento definisce i flussi operativi principali della piattaforma Stella prima dell'implementazione reale.

L'obiettivo e trasformare la documentazione esistente in una struttura operativa chiara, utile per:

- definire il perimetro definitivo del prodotto
- allineare ecommerce, backoffice e processi interni
- evidenziare schermate mancanti
- evidenziare moduli mancanti nel backoffice

## Fonti usate

Questo documento e stato ricavato da:

- specifica completa di architettura e dominio
- specifica database
- stato attuale del backend
- contesto prodotto Stella

## Principi trasversali

Prima dei singoli flussi, restano valide queste regole di sistema:

1. Il Core backend e l'unica fonte di verita per prodotti, lotti, stock, ordini, qualita e audit.
2. Il flag BIO e una proprieta del lotto, non del prodotto.
3. Le giacenze derivano dai movimenti di magazzino.
4. Un lotto bloccato non puo essere lavorato, confezionato, assegnato o venduto.
5. Le informazioni esposte via QR devono essere filtrate e sicure.
6. Audit funzionale e logging tecnico restano separati.

---

## 1. Flusso ecommerce B2C

### Obiettivo

Permettere a un cliente privato di consultare il catalogo, acquistare prodotti Stella, completare il checkout e seguire l'ordine.

### Attori coinvolti

- cliente B2C
- backend ecommerce
- sistema ordini
- magazzino
- amministrazione / fatturazione

### Entita principali

- Product
- Customer
- CustomerAddress
- Order
- OrderItem
- StockReservation
- Batch
- InventoryMovement

### Passaggi operativi

1. Il cliente consulta il catalogo disponibile per il canale B2C.
2. Seleziona prodotto, formato e quantita.
3. Aggiunge i prodotti al carrello.
4. Inserisce dati cliente e indirizzo di spedizione/fatturazione.
5. Sceglie se acquistare come ospite o con account.
6. Il backend valida disponibilita, prezzi e dati checkout.
7. Il sistema crea ordine e righe ordine.
8. Il sistema crea la riserva stock.
9. Parte il flusso pagamento esterno.
10. Alla conferma del pagamento, l'ordine passa a `pagato`.
11. L'ordine entra in preparazione magazzino.
12. Dopo picking e imballo, l'ordine passa a `spedito`.
13. A chiusura completata, l'ordine passa a `chiuso`.

### Schermate mancanti

- pagina carrello definitiva
- checkout completo ospite / registrato
- pagina conferma ordine
- area cliente con storico ordini
- dettaglio ordine con stato e tracking

### Moduli mancanti nel backoffice

- gestione ordini B2C
- coda ordini da preparare
- gestione riserve stock
- pannello stati pagamento
- pannello storico cliente B2C

---

## 2. Flusso commerciale B2B

### Obiettivo

Gestire clienti business, richieste commerciali, listini, ordini B2B e forniture ricorrenti.

### Attori coinvolti

- cliente B2B
- commerciale
- admin
- magazzino
- amministrazione

### Entita principali

- Customer
- CustomerAddress
- Product
- Order
- OrderItem
- StockReservation
- listino B2B (da definire come entita o regola)
- condizioni commerciali (da definire)

### Passaggi operativi

1. Il cliente business chiede informazioni o listino.
2. Il commerciale crea o aggiorna l'anagrafica cliente.
3. Il commerciale definisce condizioni commerciali e prodotti disponibili.
4. Il cliente invia una richiesta o un ordine B2B.
5. Il commerciale verifica disponibilita, tempi, formati e eventuali deroghe.
6. Il sistema registra l'ordine B2B.
7. Il backend crea righe e riserve stock.
8. L'ordine entra in lavorazione logistica.
9. A spedizione avvenuta, il flusso passa ad amministrazione per documenti e fattura.

### Schermate mancanti

- anagrafica cliente B2B completa
- vista listini e condizioni commerciali
- richiesta fornitura / preventivo
- inserimento ordine B2B da operatore
- storico ordini B2B e frequenza acquisto

### Moduli mancanti nel backoffice

- CRM commerciale base
- listini B2B
- condizioni pagamento B2B
- ordini business
- richieste fornitura / preventivi
- note commerciali e assegnazione account

---

## 3. Flusso operativo di magazzino

### Obiettivo

Gestire carichi, scarichi, prelievi, riserve, scarti e spedizioni mantenendo la giacenza coerente per prodotto e lotto.

### Attori coinvolti

- magazzino
- produzione
- admin
- qualita

### Entita principali

- Batch
- InventoryMovement
- StockReservation
- Product
- Order

### Passaggi operativi

1. Il magazzino registra carichi da ricezione o da produzione.
2. Il sistema crea movimenti con tipo e causale.
3. Le giacenze disponibili vengono derivate dai movimenti registrati.
4. Le riserve stock bloccano parte della disponibilita per ordini o preallocazioni.
5. Il magazzino effettua picking su ordini confermati.
6. Il sistema consuma o rilascia la riserva stock.
7. Eventuali scarti o rettifiche vengono registrati con causale e audit.
8. La disponibilita finale resta coerente con i movimenti del lotto.

### Schermate mancanti

- dashboard disponibilita per prodotto e lotto
- registro movimenti magazzino
- vista riserve attive
- schermata picking / preparazione ordine
- schermata rettifica manuale

### Moduli mancanti nel backoffice

- modulo inventory operativo
- vista disponibilita vendibile
- gestione riserve
- picking list
- rettifiche stock con audit

---

## 4. Gestione lotti

### Obiettivo

Gestire il ciclo di vita del lotto dalla materia prima al confezionato, mantenendo genealogia, stato e idoneita operativa.

### Attori coinvolti

- magazzino
- produzione
- qualita
- admin

### Entita principali

- Batch
- BatchLink
- Product
- Supplier
- Certification
- QualityCheck
- NonConformity

### Passaggi operativi

1. Viene registrato un raw batch in ingresso.
2. Il lotto riceve codice univoco, prodotto, fornitore, date, flag BIO e stato iniziale.
3. In caso di trasformazione, vengono selezionati uno o piu lotti input.
4. Il sistema genera un process batch.
5. Il sistema crea i legami genealogici in `BatchLink`.
6. In fase di confezionamento, viene creato un packed batch.
7. Il lotto puo passare tra stati:
   - creato
   - in_verifica
   - conforme
   - bloccato
   - esaurito
   - archiviato
8. Se il lotto viene bloccato, non puo entrare in altri flussi attivi.

### Schermate mancanti

- anagrafica lotto completa
- vista genealogia lotto
- creazione raw batch
- creazione process batch
- creazione packed batch
- storico transizioni stato lotto

### Moduli mancanti nel backoffice

- modulo batches completo
- genealogia lotti
- gestione stati lotto
- verifica validita BIO collegata a fornitore/certificazione

---

## 5. Tracciabilita QR

### Obiettivo

Consentire la consultazione pubblica e sicura della tracciabilita del prodotto confezionato tramite QR.

### Attori coinvolti

- utente pubblico
- cliente finale
- backend traceability
- admin / qualita per gestione dati esposti

### Entita principali

- Batch
- PublicTraceView
- Product
- BatchLink

### Passaggi operativi

1. In fase di confezionamento viene creato il packed batch.
2. Il sistema genera il QR associato al lotto confezionato.
3. Il QR punta a una vista o endpoint pubblico di tracciabilita.
4. L'utente scansiona il codice.
5. Il backend recupera i dati safe esponibili.
6. Il sistema mostra:
   - codice lotto
   - prodotto
   - origine
   - eventuale informazione BIO esponibile
   - date e informazioni pubbliche essenziali
7. I dati sensibili interni non vengono mostrati.

### Schermate mancanti

- pagina pubblica di consultazione QR
- configurazione dati esponibili
- storico scansioni QR (eventuale futuro)

### Moduli mancanti nel backoffice

- gestione QR per packed batch
- configurazione vista pubblica
- validazione contenuti pubblici

---

## 6. Gestione ordini

### Obiettivo

Gestire l'intero ciclo dell'ordine, dalla creazione alla chiusura, per B2C e B2B.

### Attori coinvolti

- cliente B2C
- cliente B2B
- commerciale
- magazzino
- amministrazione
- admin

### Entita principali

- Order
- OrderItem
- Customer
- CustomerAddress
- StockReservation
- Product
- Batch

### Passaggi operativi

1. L'ordine viene creato dal checkout B2C o da flusso commerciale B2B.
2. Il sistema valida dati cliente, righe, prezzi e disponibilita.
3. Il sistema crea ordine e righe.
4. Viene creata la riserva stock.
5. L'ordine attraversa gli stati principali:
   - creato
   - in_attesa_pagamento
   - pagato
   - in_preparazione
   - spedito
   - chiuso
   - annullato
   - fallito
6. In caso di fallimento o annullamento, la riserva viene rilasciata.
7. In caso di avanzamento corretto, l'ordine passa a logistica e poi a amministrazione.

### Schermate mancanti

- lista ordini filtrabile per stato
- dettaglio ordine completo
- timeline stati ordine
- vista anomalie ordine
- gestione annullamenti / fallimenti

### Moduli mancanti nel backoffice

- order management completo
- cambio stato ordine guidato
- audit cambi stato
- rilascio manuale riserve
- ricerca ordini per cliente e periodo

---

## 7. Gestione spedizioni

### Obiettivo

Gestire preparazione, uscita e chiusura logistica dell'ordine.

### Attori coinvolti

- magazzino
- amministrazione
- corriere / servizio esterno
- cliente finale

### Entita principali

- Order
- OrderItem
- CustomerAddress
- Batch
- InventoryMovement

### Passaggi operativi

1. Il magazzino riceve l'ordine `pagato` o autorizzato.
2. Effettua picking delle righe.
3. Prepara imballo e assegna i colli.
4. Registra l'uscita di magazzino.
5. Inserisce dati spedizione e tracking.
6. L'ordine passa a `spedito`.
7. A consegna confermata o chiusura amministrativa, l'ordine passa a `chiuso`.

### Schermate mancanti

- coda ordini da spedire
- schermata picking/imballo
- inserimento tracking
- storico spedizioni

### Moduli mancanti nel backoffice

- shipping desk
- gestione tracking
- uscita merce collegata ai movimenti
- filtro ordini pronti da spedire

---

## 8. Generazione fatture

### Obiettivo

Gestire la predisposizione amministrativa del documento fiscale in coerenza con ordini e clienti.

### Attori coinvolti

- amministrazione
- commerciale
- cliente
- backend ordini

### Entita principali

- Order
- Customer
- CustomerAddress
- dati fiscali cliente
- documento fattura (entita da formalizzare)

### Passaggi operativi

1. L'ordine viene confermato come pagato o validato commercialmente.
2. Il sistema recupera dati cliente e indirizzo di fatturazione.
3. L'amministrazione verifica imponibili, imposte e condizioni.
4. Viene generata la fattura o il documento equivalente.
5. Il documento viene collegato all'ordine.
6. Lo stato amministrativo dell'ordine viene aggiornato.

### Schermate mancanti

- pannello fatturazione ordine
- anagrafica fiscale cliente completa
- storico documenti emessi
- export amministrativo

### Moduli mancanti nel backoffice

- modulo fatture / documenti
- dati fiscali cliente strutturati
- stato fatturazione ordine
- export per amministrazione

### Nota

La documentazione attuale parla chiaramente di ordini e dati cliente, ma la parte fatture non e ancora formalizzata a livello di modulo e modello dati come le altre aree. Va quindi definita meglio prima dell'implementazione.

---

## 9. Flusso qualita e controlli

### Obiettivo

Garantire che lotti e processi siano verificati, bloccando tempestivamente materiale non conforme.

### Attori coinvolti

- qualita
- admin
- produzione
- magazzino

### Entita principali

- Batch
- QualityCheck
- NonConformity
- Certification
- Supplier

### Passaggi operativi

1. L'utente qualita apre un controllo sul lotto.
2. Il sistema propone checklist e versione di controllo.
3. L'utente registra esito:
   - ok
   - warning
   - ko
4. In caso di esito negativo, il sistema puo:
   - bloccare il lotto
   - aprire una non conformita
5. La non conformita segue il suo ciclo:
   - aperta
   - in_analisi
   - in_risoluzione
   - chiusa
6. La chiusura richiede motivazione e responsabilita.
7. Il lotto puo essere sbloccato solo da ruoli abilitati e con esito coerente.

### Schermate mancanti

- registro controlli qualita
- apertura controllo su lotto
- checklist compilabile
- registro non conformita
- dettaglio blocco/sblocco lotto

### Moduli mancanti nel backoffice

- quality checks
- non conformities
- blocco/sblocco lotto
- checklist versionate
- storico certificazioni e validita BIO

---

## 10. Schermate minime da prevedere nel backoffice

Per rendere la piattaforma davvero operativa, il backoffice definitivo dovrebbe avere almeno queste sezioni:

### Anagrafiche

- prodotti
- fornitori
- clienti
- indirizzi clienti
- certificazioni

### Operativita

- lotti
- genealogia lotti
- movimenti di magazzino
- riserve stock
- ordini
- spedizioni

### Qualita

- controlli qualita
- non conformita
- blocchi lotto

### Commerciale

- richieste B2B
- listini e condizioni commerciali
- ordini business

### Tracciabilita

- gestione packed batch
- generazione QR
- configurazione vista pubblica QR

### Amministrazione

- dati fiscali cliente
- stato fatturazione
- documenti emessi

---

## 11. Moduli mancanti da formalizzare prima dello sviluppo

La documentazione attuale e forte su dominio e dati, ma ci sono ancora moduli da formalizzare meglio:

1. modulo fatturazione
2. modulo spedizioni con tracking
3. modulo richieste commerciali B2B
4. strategia di assegnazione lotto alle righe ordine
5. timeout e rilascio riserve checkout B2C
6. politiche multi-magazzino o magazzino singolo
7. struttura definitiva delle checklist qualita

---

## 12. Lettura finale

La piattaforma Stella non e solo un ecommerce.

La struttura definitiva del prodotto richiede l'integrazione coerente di:

- canale di vendita
- gestione ordini
- magazzino
- lotti
- qualita
- tracciabilita
- supporto commerciale B2B
- supporto amministrativo

Questo documento definisce quindi il perimetro operativo minimo della piattaforma e puo essere usato come base per la progettazione definitiva del backoffice e del backend.
