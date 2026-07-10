# booktracker-web-app

Piattaforma web per tracciare e gestire la propria libreria di libri letti.

## Overview

# Product Requirements Document (PRD)  
## booktracker-web-app

---

## 1. Project Overview

**BookTracker** è una piattaforma web progettata per consentire agli utenti di creare e gestire una libreria personale, monitorare i libri letti, annotare progressi di lettura, e condividere selettivamente recensioni e statistiche. L’obiettivo è offrire uno strumento semplice, intuitivo e sicuro, superando la complessità di soluzioni esistenti come fogli di calcolo o app troppo articolate. La piattaforma include funzionalità di moderazione per garantire la qualità dei contenuti pubblici e la sicurezza della community.

---

## 2. Goals & Success Metrics

### Obiettivi Principali

- Fornire un’esperienza utente intuitiva per la gestione della propria libreria e dei progressi di lettura.
- Consentire la condivisione selettiva di libreria, recensioni e statistiche.
- Offrire strumenti di moderazione efficaci per gli amministratori.
- Garantire la sicurezza, la privacy e la conformità normativa (es. GDPR).

### Success Metrics (KPI)

- **Registrazione e accesso**: ≥ 95% degli utenti riesce a completare la registrazione e il login senza errori.
- **Gestione libreria**: ≥ 90% degli utenti aggiunge almeno un libro entro la prima settimana.
- **Performance**: Tempo di risposta delle API < 2 secondi per il 99% delle richieste.
- **Utilizzo import/export**: ≥ 30% degli utenti utilizza almeno una volta la funzione di import/export nei primi 3 mesi.
- **Moderazione**: Tutti i contenuti segnalati vengono revisionati da un amministratore entro 24 ore.
- **Privacy**: Nessun dato privato viene esposto pubblicamente senza consenso esplicito dell’utente.
- **Soddisfazione utente**: Punteggio medio di soddisfazione ≥ 4/5 nelle survey trimestrali.

---

## 3. Target Users

### 3.1 Utente Finale (Reader)

- **Bisogni**:
  - Gestire facilmente la propria libreria digitale.
  - Monitorare i libri letti, in lettura e da leggere.
  - Annotare progressi, recensioni e voti.
  - Importare/esportare dati.
  - Condividere selettivamente contenuti e statistiche.
  - Controllare la privacy dei propri dati.

### 3.2 Amministratore

- **Bisogni**:
  - Gestire e moderare contenuti pubblici (recensioni, profili, immagini).
  - Gestire segnalazioni e sospendere utenti se necessario.
  - Accedere a log di moderazione e audit trail.

---

## 4. Core Features

### 4.1 Gestione Account

- **Registrazione e autenticazione** (via email/password, Laravel Sanctum).
- **Modifica profilo** (nome, email, immagine profilo opzionale).
- **Recupero password** (email di reset).
- **Impostazioni privacy** (configurazione visibilità libreria, recensioni, statistiche).
- **Gestione sessioni e logout**.

### 4.2 Gestione Libri

- **Aggiunta libro** (titolo, autore, stato, date inizio/fine, voto, recensione, copertina opzionale).
- **Modifica/eliminazione libro**.
- **Stato libro**: Da leggere / In lettura / Completato.
- **Gestione progressi**: inserimento e aggiornamento date di inizio/fine.
- **Voto e recensione personale**:
  - Recensione e voto privati per default.
  - Possibilità di rendere pubblica la recensione/voto (modificabile in ogni momento).
- **Ricerca e filtri**:
  - Ricerca per titolo/autore.
  - Filtri per stato e valutazione.
- **Import/export**:
  - Importazione libreria da CSV.
  - Esportazione dati in CSV/JSON.

### 4.3 Dashboard Utente

- **Statistiche personali**:
  - Numero totale di libri.
  - Libri letti nell’anno corrente.
  - Tempo medio di lettura (calcolato automaticamente da date inizio/fine).
  - Ultimi libri aggiunti.
- **Visualizzazione e condivisione**:
  - Possibilità di rendere pubbliche statistiche e/o libreria.
  - Condivisione tramite link pubblico configurabile.

### 4.4 Moderazione e Amministrazione

- **Gestione contenuti pubblici**:
  - Visualizzazione e moderazione di recensioni pubbliche, commenti, descrizioni personalizzate, immagini profilo.
  - Gestione segnalazioni da parte della community.
  - Sospensione utenti e gestione profili segnalati.
- **Audit log**:
  - Tracciamento di tutte le azioni amministrative e di moderazione.

### 4.5 Sicurezza e Privacy

- **Controllo accessi**: Ruoli (utente, amministratore) e permessi granulari.
- **Visibilità selettiva**: Privacy configurabile per ogni tipologia di dato condiviso.
- **Protezione API**: Autenticazione, rate limiting, HTTPS obbligatorio.
- **Gestione segnalazioni**: Sistema di report per contenuti pubblici.

---

## 5. Technical Architecture

### 5.1 Stack Tecnologico

- **Backend**: Laravel (PHP)
- **Frontend**: Vue.js (SPA)
- **Database**: MySQL
- **Autenticazione**: Laravel Sanctum
- **Containerizzazione**: Docker

### 5.2 Data Models (Schema Principali)

#### Utente

| Campo         | Tipo      | Note                       |
|---------------|-----------|----------------------------|
| id            | int       | PK                         |
| nome          | string    |                            |
| email         | string    | univoco                    |
| password      | string    | cifrato                    |
| ruolo         | enum      | utente / amministratore    |
| created_at    | datetime  |                            |

#### Libro

| Campo         | Tipo      | Note                       |
|---------------|-----------|----------------------------|
| id            | int       | PK                         |
| utente_id     | int       | FK → Utente                |
| titolo        | string    |                            |
| autore        | string    |                            |
| stato         | enum      | Da leggere / In lettura / Completato |
| voto          | int       | 1-5                        |
| data_inizio   | date      | opzionale                  |
| data_fine     | date      | opzionale                  |

#### Recensione

| Campo         | Tipo      | Note                       |
|---------------|-----------|----------------------------|
| id            | int       | PK                         |
| libro_id      | int       | FK → Libro                 |
| utente_id     | int       | FK → Utente                |
| testo         | text      |                            |
| voto          | int       | 1-5                        |
| visibilità    | enum      | privata / pubblica         |

#### ImpostazioniPrivacy

| Campo                | Tipo      | Note           |
|----------------------|-----------|----------------|
| id                   | int       | PK             |
| utente_id            | int       | FK → Utente    |
| libreria_pubblica    | boolean   |                |
| statistiche_pubbliche| boolean   |                |
| recensioni_pubbliche | boolean   |                |

#### LogModerazione

| Campo             | Tipo      | Note                  |
|-------------------|-----------|-----------------------|
| id                | int       | PK                    |
| amministratore_id | int       | FK → Utente           |
| utente_id         | int       | FK → Utente           |
| azione            | string    | descrizione azione    |
| motivazione       | text      |                       |
| created_at        | datetime  |                       |

### 5.3 Relazioni

- Un utente possiede molti libri (1:N).
- Un libro può avere una recensione associata (1:1).
- Un utente possiede una sola configurazione di privacy (1:1).
- Un amministratore può generare molti log di moderazione (1:N).

### 5.4 API REST Principali

#### Account & Privacy

- `POST /api/register`
- `POST /api/login`
- `POST /api/password/forgot`
- `PATCH /api/profile`
- `PATCH /api/privacy`

#### Libreria & Libri

- `GET /api/library`
- `POST /api/library`
- `PATCH /api/library/{id}`
- `DELETE /api/library/{id}`
- `POST /api/library/import`
- `GET /api/library/export`
- `GET /api/library/public/{user}`

#### Recensioni

- `POST /api/reviews`
- `PATCH /api/reviews/{id}`
- `DELETE /api/reviews/{id}`

#### Statistiche

- `GET /api/statistics`
- `GET /api/statistics/public/{user}`

#### Moderazione (Admin)

- `GET /api/admin/reports`
- `GET /api/admin/reviews`
- `PATCH /api/admin/reviews/{id}`
- `DELETE /api/admin/reviews/{id}`
- `PATCH /api/admin/users/{id}/suspend`

---

## 6. Non-Functional Requirements

- **Interfaccia responsive**: Usabilità ottimale su desktop, tablet, mobile.
- **Performance**: Tempo di risposta delle API < 2 secondi.
- **Sicurezza**:
  - Autenticazione sicura (Laravel Sanctum).
  - Password cifrate.
  - Solo HTTPS.
  - Rate limiting sulle API.
  - Protezione contro XSS, CSRF, SQL Injection.
- **Privacy**:
  - Modello di privacy configurabile.
  - Audit log delle operazioni amministrative.
  - Conformità GDPR.
- **Scalabilità**:
  - Architettura modulare e containerizzata (Docker).
  - Supporto a più istanze backend/frontend.
- **Affidabilità**:
  - Backup regolari del database.
  - Monitoraggio errori e logging centralizzato.
- **Internazionalizzazione**:
  - Pronto per localizzazione multilingua (i18n).

---

## 7. Out of Scope (v1)

- Integrazione con servizi esterni di catalogazione libri (es. Google Books, Open Library).
- Funzionalità social avanzate (messaggi privati, gruppi di lettura, feed attività).
- Notifiche push/email per eventi (es. nuovi libri aggiunti da amici).
- App mobile native (solo web responsive in v1).
- Statistiche avanzate (es. analisi grafiche dettagliate, trend di lettura).
- Gestione prestiti/condivisione fisica di libri.
- Supporto a formati di import/export diversi da CSV/JSON.

---

## 8. Open Questions

- **Gestione immagini**: L’upload di immagini di copertina libro e profilo sarà disponibile in v1? Se sì, quali limiti/formati sono previsti?
- **Moderazione automatica**: È prevista una prima analisi automatica (es. filtri linguaggio offensivo) o solo moderazione manuale?
- **Ruoli amministrativi**: Sono previsti più livelli di amministrazione (es. moderatore vs super-admin)?
- **Versionamento API**: Si desidera prevedere versionamento esplicito delle API REST (es. `/api/v1/`) già dalla v1?
- **Soglia di libri/recensioni pubblicabili**: Sono previsti limiti quantitativi per evitare spam?
- **Gestione dati eliminati**: I dati eliminati (libri, recensioni) vengono soft-deleted per eventuale recupero/admin audit?

---

**Nota:** Questo documento rappresenta la base per la progettazione e lo sviluppo della piattaforma booktracker-web-app. Tutte le specifiche dovranno essere validate e dettagliate ulteriormente in fase di analisi tecnica e design UI/UX.