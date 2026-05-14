# Revisione V1 Minimale del Backoffice Stella

## Obiettivo

Trasformare il prototipo backoffice Stella da interfaccia troppo ampia e pesante a strumento operativo semplice, chiaro e guidato.

Il backoffice V1 non deve sembrare:

- un ERP complesso
- un gestionale enterprise
- una dashboard piena di KPI
- una raccolta di moduli paralleli

Deve sembrare:

- uno strumento operativo quotidiano
- semplice da leggere
- chiaro nelle priorita
- elegante e sobrio
- coerente con Stella

---

## 1. Cosa eliminare

## Da eliminare subito

- KPI ridondanti o troppo numerosi nella dashboard
- pannelli che spiegano il sistema invece di guidare l'azione
- doppie letture della stessa informazione tra dashboard, magazzino e lotti
- moduli con troppo peso visivo ma poco valore V1
- eccesso di badge, timeline e box nella stessa schermata
- testi descrittivi lunghi
- linguaggio troppo da piattaforma complessa

## Da togliere o ridurre fortemente

- dashboard con piu livelli contemporanei
- tabelle grandi affiancate ad altri pannelli complessi
- sidebar con troppe voci
- schermate che mescolano overview, dettaglio, alert, report e configurazione nello stesso spazio

---

## 2. Cosa semplificare

## Dashboard

La dashboard deve rispondere solo a:

**Cosa devo fare oggi?**

Massimo 4 o 5 azioni:

- Registra merce in ingresso
- Controlla lotti
- Gestisci ordini
- Registra vendita
- Genera QR

Non deve essere una dashboard analitica.

## Merce

La schermata deve avere una funzione sola:

**registrare nuova merce in ingresso**

Elementi minimi:

- CTA primaria: `+ Registra ingresso`
- tabella semplice
- stato essenziale

Niente layer secondari troppo pesanti.

## Lotti

La schermata lotti deve mostrare solo:

- codice lotto
- tipo
- disponibilita
- stato
- BIO / non BIO
- QR
- movimenti principali

Non serve un dettaglio troppo tecnico in prima vista.

## Vendite

B2C e B2B stanno nello stesso flusso.

Quello che cambia e:

- B2C -> spedizione e QR
- B2B -> documento / fattura

Non serve dividere il magazzino o i lotti per canale.

Regola operativa:

- ogni vendita deve restare collegata al lotto realmente usato
- per il B2C il backend propone il lotto con logica FIFO semplice
- l'operatore puo confermare o cambiare lotto

## Clienti

Separare:

- privati ecommerce
- aziende ingrosso

Ma con UI leggera:

- lista
- filtro tipo cliente
- pannello dettaglio semplice

## QR

La schermata deve sembrare:

- premium
- semplice
- reale

Non tecnica.

---

## 3. Cosa accorpare

## Accorpamenti consigliati

### 1. Ingressi + registro primi movimenti

Non servono come due mondi separati nella V1.

La schermata `Merce` puo coprire:

- registrazione ingresso
- creazione lotto
- primo movimento
- stato iniziale

### 2. Ordini + vendita

Nel prototipo la V1 puo usare una sola schermata:

- ordini ecommerce
- ordini ingrosso
- stato vendita
- scarico collegato

### 3. Magazzino + disponibilita

La sezione `Magazzino` non deve sembrare un modulo tecnico di stock control.

Deve mostrare:

- disponibile
- riservato
- in lavorazione

in una forma molto leggibile.

### 4. Fatture base

Per la V1 serve un modulo chiaro:

- `Fatture`

solo per l'ingrosso.

---

## 4. Nuova gerarchia UI

## Sidebar minima

- Dashboard
- Merce
- Lotti
- Magazzino
- Vendite
- Clienti
- Fatture
- Tracciabilita

## Ordine mentale corretto

1. entra la merce
2. nasce il lotto
3. si aggiorna il magazzino
4. si vende
5. si scarica
6. si genera QR
7. si chiude la fattura se ingrosso

---

## 5. Wireframe piu semplici

## 5.1 Dashboard

### Struttura

- titolo
- breve frase di contesto
- 4/5 action card grandi
- una lista breve di priorita

### Esempio

- `Registra merce in ingresso`
- `Controlla lotti`
- `Gestisci ordini`
- `Registra vendita`
- `Apri tracciabilita`

Niente KPI sparsi tutto attorno.

La dashboard deve mostrare solo:

- ordini ecommerce da preparare
- ordini B2B aperti
- lavorazioni aperte
- lotti bloccati
- ingressi recenti
- recap mattina
- recap sera

---

## 5.2 Merce

### Header

- titolo: `Merce`
- CTA primaria: `+ Registra ingresso`

### Corpo

Tabella semplice:

`Data | Fornitore | Tipo | BIO | Quantita | Lotto | Stato`

Il form di registrazione si apre in modal:

- fornitore
- data
- tipo merce
- varieta
- quantita
- unita di misura
- BIO / non BIO
- certificazione
- documento
- note

Regola:

**registrazione ingresso = creazione lotto**

---

## 5.3 Lotti

### Header

- titolo: `Lotti`
- ricerca
- filtro stato

### Corpo

Lista pulita:

`Codice lotto | Tipo | Disponibile | Stato | BIO | QR`

### Apertura dettaglio

Nel dettaglio:

- header semplice
- disponibilita
- azioni essenziali
- timeline operativa

Niente schermata sovraccarica.

---

## 5.4 Magazzino

### Header

- titolo: `Magazzino`

### Corpo

Tabella unica, a tutta larghezza:

`Lotto | Prodotto | Disponibile | Riservato | Ubicazione | Stato`

---

## 5.5 Vendite

### Header

- titolo: `Vendite`
- CTA: `+ Registra vendita`

### Corpo

tabella unica:

`Ordine | Cliente | Tipo | Lotto | Quantita | Stato | Fattura`

Filtro:

- B2C
- B2B

### Regola UX

Non fare due schermate diverse per B2C e B2B.

Usare:

- lista
- ricerca
- drawer dettaglio

---

## 5.6 Clienti

### Header

- titolo: `Clienti`

### Corpo

tab semplice:

- B2C
- B2B

Lista essenziale:

`Cliente | Ultimo ordine | Stato`

Dettaglio:

- privati -> dati base
- aziende -> ragione sociale, P.IVA, PEC, SDI, storico

---

## 5.7 Fatture

### Header

- titolo: `Fatture`

### Corpo

tabella:

`Fattura | Cliente | Lotto | Quantita | Stato | Data`

CTA:

- `Nuova fattura`

---

## 5.8 Tracciabilita

### Header

- titolo: `Tracciabilita`

### Corpo

lista semplice:

`Lotto | Prodotto | Vista pubblica | Stato`

Dettaglio:

- QR
- filiera
- storico lotto
- certificazioni
- origine merce

Niente taglio tecnico o da pannello IT.

---

## 6. Riduzione della complessita visiva

## Regole visive

- una funzione principale per schermata
- una CTA primaria
- una tabella o una lista principale
- un solo dettaglio aperto alla volta
- meno box
- meno contrasti inutili
- piu spazio bianco
- meno testo
- meno KPI

## Da evitare

- 3 pannelli complessi affiancati
- KPI + tabella + timeline + code nella stessa vista
- piu CTA con lo stesso peso
- testi che spiegano il senso del modulo

---

## 7. Identita premium Stella

Per restare coerenti col brand:

- colori morbidi e controllati
- gerarchia tipografica calma
- superfici chiare e pulite
- pochi accenti scuri
- niente effetto software enterprise freddo
- niente look corporate ERP

La sensazione deve essere:

- precisa
- elegante
- essenziale
- sicura

---

## 8. Sintesi finale

La direzione giusta non e aggiungere moduli.

La direzione giusta e:

- togliere
- accorpare
- chiarire
- guidare

La V1 del backoffice Stella deve ruotare attorno a:

- merce
- lotti
- disponibilita
- vendite
- documenti
- tracciabilita

Con una forma piu semplice, piu umana e molto meno ERP.
