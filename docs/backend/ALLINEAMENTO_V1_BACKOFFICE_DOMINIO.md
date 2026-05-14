# Allineamento V1 Backoffice e Dominio

## Scopo

Questo documento allinea la **V1 del backoffice Stella** con il dominio e le API gia presenti nel repository di codice:

- [Stella-Code](/C:/Users/ricca/Documents/Playground/Stella-Code)

La priorita della V1 non e l'ecommerce in senso stretto, ma il flusso operativo:

**Entrata merce -> Lotto -> Movimenti / lavorazioni -> Disponibilita magazzino -> Vendita ecommerce / ingrosso -> Scarico -> QR -> Fattura solo se ingrosso**

Regola chiave V1:

**registrazione ingresso = creazione lotto**

Non deve esistere una seconda operazione manuale di creazione lotto.

---

## 1. Mappa tra flusso V1 e codice esistente

| Flusso V1 | Dominio/API gia presenti | Note |
|---|---|---|
| Entrata merce | `Supplier`, `SupplierDocument`, `Certification`, `Batch`, `InventoryMovement`; [SuppliersController.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Api/Controllers/SuppliersController.cs), [BatchesController.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Api/Controllers/BatchesController.cs), [InventoryMovementsController.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Api/Controllers/InventoryMovementsController.cs) | I mattoni ci sono, ma non esiste ancora un caso d'uso unico `Registra ingresso merce` che crei lotto e movimento in automatico. |
| Lotto | `Batch`; [Batch.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/Batch.cs), [CreateBatchCommand.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Batches/Commands/CreateBatchCommand.cs), [BatchDto.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Batches/Models/BatchDto.cs) | Il lotto esiste gia come entita centrale, ma e ancora povero rispetto ai dati operativi richiesti dalla V1. |
| Movimenti | `InventoryMovement`; [InventoryMovement.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/InventoryMovement.cs), [CreateInventoryMovementCommand.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Inventory/Commands/CreateInventoryMovementCommand.cs), [InventoryMovementRepository.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Infrastructure/Repositories/InventoryMovementRepository.cs) | Esiste il registro movimenti e il calcolo saldo, ma i tipi movimento oggi sono ancora tecnici/generici. |
| Magazzino / disponibilita | [GetInventoryBalanceByBatchQueryHandler.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Inventory/Handlers/GetInventoryBalanceByBatchQueryHandler.cs), [GetInventoryBalanceByProductQueryHandler.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Inventory/Handlers/GetInventoryBalanceByProductQueryHandler.cs) | Oggi viene calcolato solo il saldo fisico da movimenti. Non esiste ancora una vista "disponibile = reale - riservato". |
| Ordini ecommerce / ingrosso | `Order`, `OrderItem`, `StockReservation`; [Order.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/Order.cs), [OrdersController.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Api/Controllers/OrdersController.cs), [StockReservationsController.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Api/Controllers/StockReservationsController.cs) | Il dominio supporta ordini e riserve, ma non orchestra ancora assegnazione lotto FIFO, scarico e collegamento ordine-lotto. |
| BIO / non BIO | `Batch.BioFlag`, `Certification`; [Batch.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/Batch.cs), [Certification.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/Certification.cs) | Il flag BIO esiste sul lotto e le certificazioni esistono sul fornitore, ma mancano regole applicative forti. |
| Tracciabilita | `PublicTraceView`, [TraceController.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Api/Controllers/TraceController.cs), [UpsertPublicTraceViewCommand.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Traceability/Commands/UpsertPublicTraceViewCommand.cs) | Esiste la vista pubblica di tracciabilita, ma e ancora sintetica e non ancora governata direttamente dal lotto realmente usato nella vendita. |
| Qualita e blocchi | `QualityCheck`, `NonConformity`; [QualityChecksController.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Api/Controllers/QualityChecksController.cs), [NonConformitiesController.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Api/Controllers/NonConformitiesController.cs) | Buona base per controlli e anomalie, ma non ancora integrata nel flusso entrata/lavorazione/disponibilita. |
| Fatturazione ingrosso | Nessuna entita o controller dedicati | Area mancante in codice. Nel prototipo backoffice esiste come modulo UI, non come backend. |

---

## 2. Parti gia implementate

## Dominio

- lotto con codice univoco, tipo, stato, flag BIO, fornitore, note e date:
  - [Batch.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/Batch.cs)
- movimenti di magazzino collegati a prodotto e lotto:
  - [InventoryMovement.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/InventoryMovement.cs)
- ordini e righe ordine:
  - [Order.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/Order.cs)
  - [OrderItem.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/OrderItem.cs)
- riserve stock separate dai movimenti:
  - [StockReservation.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/StockReservation.cs)
- fornitori, documenti fornitore e certificazioni:
  - [Supplier.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/Supplier.cs)
  - [SupplierDocument.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/SupplierDocument.cs)
  - [Certification.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/Certification.cs)
- controlli qualita e non conformita:
  - [QualityCheck.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/QualityCheck.cs)
  - [NonConformity.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/NonConformity.cs)
- vista pubblica di tracciabilita:
  - [PublicTraceView.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Domain/Entities/PublicTraceView.cs)

## API / Application

- CRUD e ricerca base per:
  - lotti
  - movimenti
  - ordini
  - riserve stock
  - fornitori
  - certificazioni
  - documenti fornitore
  - controlli qualita
  - non conformita
  - tracciabilita pubblica
- verifica referenziale minima gia presente in alcuni handler:
  - [CreateBatchCommandHandler.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Batches/Handlers/CreateBatchCommandHandler.cs)
  - [CreateInventoryMovementCommandHandler.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Inventory/Handlers/CreateInventoryMovementCommandHandler.cs)
  - [CreateOrderCommandHandler.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/Orders/Handlers/CreateOrderCommandHandler.cs)
  - [CreateStockReservationCommandHandler.cs](/C:/Users/ricca/Documents/Playground/Stella-Code/Mandorle.Application/StockReservations/Handlers/CreateStockReservationCommandHandler.cs)

## Prototipo backoffice

Nel prototipo V1 consolidato la struttura minima e:

- Dashboard
- Merce
- Magazzino
- Lotti
- Fatture
- Vendite
- Clienti
- Tracciabilita

Regole UX fissate:

- clienti separati solo come anagrafica (`B2C` / `B2B`)
- stock e vendite nello stesso flusso
- tabelle operative a tutta larghezza
- dettaglio in drawer / popup
- `QR` non come modulo isolato, ma dentro `Tracciabilita`

Riferimenti:

- [Stella-Backoffice-Prototype.html](/C:/Users/ricca/Documents/Playground/Documentazione/prototypes/backoffice/Stella-Backoffice-Prototype.html)
- [PIATTAFORMA_OPERATIVA_BACKOFFICE.md](/C:/Users/ricca/Documents/Playground/Documentazione/docs/backoffice/PIATTAFORMA_OPERATIVA_BACKOFFICE.md)

---

## 3. Parti mancanti

## 3.1 Caso d'uso unico "Entrata merce"

Manca una API/command applicativa che faccia insieme:

1. validazione fornitore
2. verifica coerenza BIO / certificazioni
3. creazione lotto automatica
4. creazione automatica movimento di ingresso
5. collegamento documenti/certificazioni
6. eventuale stato iniziale del lotto

Oggi queste operazioni esistono solo come pezzi separati.

Questa e la prima priorita backend della V1.

## 3.2 Lotto troppo povero per la V1

Nel lotto oggi mancano come dati espliciti:

- varieta mandorla dedicata
- quantita iniziale
- unita di misura sul lotto
- quantita disponibile
- quantita riservata
- collegamento diretto a documenti lotto
- stato qualita iniziale

Alcuni di questi valori possono essere derivati, ma per il backoffice V1 servono in una vista DTO/servizio piu ricca.

## 3.3 Logica disponibilita incompleta

La V1 richiede:

`stock disponibile = stock reale - stock riservato`

Oggi:

- il saldo fisico viene calcolato dai movimenti
- le riserve sono salvate separatamente
- non esiste ancora un servizio/query che unisca le due cose

Quindi manca la vera nozione di **disponibile operativo**.

## 3.4 Movimenti non allineati al linguaggio V1

La repository dei movimenti usa oggi tipi come:

- `LOAD`
- `UNLOAD`
- `RETURN_IN`
- `RETURN_OUT`
- `TRANSFER_OUT`
- `ADJUSTMENT_IN`
- `ADJUSTMENT_OUT`
- `WASTE`

Per la V1 servono invece tipi piu aderenti al dominio Stella:

- Ingresso
- Lavorazione
- Calo / scarto
- Confezionamento
- Vendita ecommerce
- Vendita ingrosso
- Rettifica
- Blocco
- Sblocco

Serve almeno una mappatura o una normalizzazione di dominio.

## 3.5 Ecommerce / ingrosso non orchestrati

Oggi:

- l'ordine si crea
- la riserva si crea
- lo stato ordine si aggiorna

Ma non esiste ancora il flusso applicativo che faccia:

- ordine ricevuto
- assegnazione lotto proposta automaticamente
- riserva stock
- pagamento/conferma
- preparazione
- spedizione
- scarico definitivo
- generazione QR dal lotto usato

Lo stesso vale per l'ingrosso:

- manca una vendita ingrosso guidata
- manca un documento associato
- manca uno scarico dedicato per il canale
- manca il collegamento esplicito vendita-lotto-fattura

## 3.6 Regole BIO mancanti

Mancano regole applicative come:

- lotto BIO obbligatoriamente con certificazione valida
- vendita BIO solo da lotti BIO
- blocco di collegamenti incoerenti tra prodotto BIO e lotto non BIO

Oggi il codice registra il dato, ma non governa davvero la regola.

## 3.7 Fatturazione ingrosso assente

Manca del tutto una base backend per:

- vendita ingrosso
- documento/fattura allegata
- stato documento
- prezzo e dati documento persistiti

Nel dominio attuale non c'e una entita `Invoice`, `SalesDocument` o simile.

## 3.8 Tracciabilita troppo sintetica

La `PublicTraceView` attuale contiene:

- batch
- nome prodotto
- flag BIO
- origine sintetica
- date sintetiche

Per la V1 servono almeno anche:

- varieta
- certificazione se presente
- lavorazioni/movimenti rilevanti
- data confezionamento o vendita
- storico essenziale dei passaggi rilevanti

La vista pubblica deve poter rispondere a:

**questa confezione / questo ordine da quale lotto proviene?**

---

## 4. Modifiche consigliate al dominio

## Priorita alta

1. Introdurre un caso d'uso applicativo `RegisterGoodsReceipt`
   - input unico per entrata merce
   - creazione batch + movimento + collegamenti documentali

2. Arricchire il lotto con dati piu vicini all'operativita
   - `Variety` o riferimento esplicito alla varieta
   - `InitialQuantity`
   - `UnitOfMeasure`
   - eventuale `PackagingDate` o `ReceivedDate`

3. Introdurre una query di disponibilita reale
   - `PhysicalStock`
   - `ReservedStock`
   - `AvailableStock`
   per lotto e per prodotto

4. Definire costanti o enum di dominio per:
   - stati lotto
   - tipi movimento
- tipo ordine
- tipo riserva

5. Introdurre assegnazione lotto FIFO semi-automatica per gli ordini ecommerce
   - stesso prodotto
   - stessa tipologia
   - stessa certificazione BIO / non BIO
   - possibilita di override manuale da backoffice

## Priorita media

6. Distinguere meglio i documenti di lotto dai documenti fornitore
- oggi i documenti stanno sul fornitore
- per la V1 e utile anche un collegamento diretto al lotto

7. Introdurre una entita minima per fatture B2B
   - collegata a ordine / vendita
   - collegata al cliente
   - collegata ai lotti usati

6. Valutare un'entita minima per vendita/documento ingrosso
   - senza fare una fatturazione fiscale completa
   - ma con persistenza di cliente, lotto, quantita, prezzo, file documento

7. Arricchire `PublicTraceView`
   - o renderla derivata da piu dati di dominio
   - per evitare popolamento troppo manuale

---

## 5. Backlog tecnico ordinato per priorita

## P0 - Necessario per una V1 coerente

1. Caso d'uso unico **Entrata merce**
2. Query/servizio **Disponibilita lotto/prodotto** = reale - riservato
3. Allineamento tipi movimento al linguaggio di dominio
4. Stato lotto V1 coerente con il backoffice:
   - Ricevuto
   - In lavorazione
   - Disponibile
   - Confezionato
   - Parzialmente venduto
   - Esaurito
   - Bloccato
5. Flusso minimo ordine ecommerce:
   - ordine
   - riserva
   - spedizione
   - scarico definitivo
6. Tracciabilita QR minima da batch venduto/confezionato

## P1 - Molto importante subito dopo

7. Regole BIO applicative
8. Schermata/API dettaglio lotto arricchita con:
   - storico movimenti
   - riserve
   - qualita
   - documenti
9. Flusso vendita ingrosso minimo
10. Documento/fattura allegata per ingrosso
11. Collegamento tra ordine e generazione QR

## P2 - Da tenere leggeri in V1 ma gia previsti

12. Vista pubblica QR piu ricca
13. Automatismi di cambio stato lotto
14. Dashboard backoffice su code operative
15. Audit piu leggibile per azioni critiche

---

## 6. Proposta di schermate backoffice minime per la V1

## 6.1 Dashboard

Serve una dashboard molto pratica, non vasta:

- entrate merce da completare
- lotti bloccati o in verifica
- ordini ecommerce in preparazione
- vendite ingrosso da chiudere
- documenti ingrosso da allegare/emettere

## 6.2 Ingressi & Movimenti

Schermata chiave della V1.

### Deve fare:

- registrare arrivo merce
- scegliere/creare fornitore
- indicare merce, varieta, quantita, BIO/non BIO
- allegare documenti
- generare lotto
- creare movimento di ingresso
- mostrare storico movimenti

## 6.3 Lotti

Schermata centrale del sistema.

### Deve mostrare:

- codice lotto
- origine / fornitore
- prodotto / varieta
- BIO / non BIO
- quantita iniziale
- quantita disponibile
- quantita riservata
- stato
- certificazioni/documenti
- storico movimenti
- controlli qualita
- QR collegato

## 6.4 Magazzino

Vista piu sintetica per operativita quotidiana.

### Deve mostrare:

- giacenze per lotto
- disponibilita reale
- riserve attive
- movimenti recenti
- lotti da bloccare o sbloccare

## 6.5 Ordini

Una sola area con distinzione chiara B2C / B2B.

### B2C

- ordine
- pagamento
- riserva
- preparazione
- spedizione
- scarico

### B2B

- cliente
- lotto selezionato
- quantita
- prezzo
- documento allegato
- eventuale QR

## 6.6 Clienti

Minimo utile per la V1:

- anagrafica B2C
- anagrafica B2B
- dati fiscali cliente ingrosso
- storico ordini

## 6.7 Qualita

Modulo leggero ma reale:

- controlli qualita per lotto
- esito
- note
- eventuale non conformita
- blocco lotto

## 6.8 QR / Tracciabilita

Serve una schermata semplice con:

- QR generato
- anteprima pubblica
- dati mostrati pubblicamente
- collegamento al lotto
- data confezionamento/vendita

## 6.9 Fatture / Documenti

Per la V1 non serve una contabilita completa.

Serve almeno:

- lista documenti ingrosso
- dettaglio documento
- file allegato o generato base
- collegamento a cliente, lotto, quantita, prezzo

---

## 7. Lettura finale

Il repository ha gia una base molto buona per costruire la V1:

- lotto
- movimenti
- riserve
- ordini
- fornitori
- certificazioni
- qualita
- tracciabilita pubblica

Quello che manca non sono tanto i "pezzi", ma i **flussi applicativi coerenti** tra questi pezzi.

La V1 quindi non va pensata come aggiunta di tanti moduli nuovi, ma come consolidamento di pochi flussi chiave:

1. entrata merce
2. gestione lotto
3. movimenti e disponibilita
4. vendita B2C/B2B sullo stesso magazzino
5. QR pubblico
6. documento ingrosso minimo

Questa e la base giusta per far diventare il backoffice Stella una piattaforma operativa vera e non solo un'estensione dell'ecommerce.
