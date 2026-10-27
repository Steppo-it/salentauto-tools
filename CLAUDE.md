# salentauto-tools

Strumenti web interni di Salentauto Srl (officina e logistica per noleggio a lungo termine, soprattutto Ayvens e Arval), Lecce.
Pubblicati su GitHub Pages: https://stefanopaianosalent.github.io/salentauto-tools/

## Come lavorare qui

- Rispondi e commenta in italiano.
- Soluzioni semplici che funzionano: niente framework, niente build, niente dipendenze nuove se non indispensabili.
- Ogni strumento è **un unico file HTML autonomo** (HTML + CSS + JS nello stesso file). Mantieni questo schema.
- Librerie esterne solo da CDN con versione fissa (es. pdf.js 3.11.174 da cdnjs).
- Nessun dato sensibile nel codice: chiavi e token Trello stanno nel `localStorage` del browser di chi usa lo strumento, inseriti dal pannello impostazioni. Il repo è pubblico.
- Pubblicazione: i file si caricano su GitHub (oggi tramite "Add files via upload"). Dopo il push, GitHub Pages aggiorna in 1-2 minuti.
- Prima di modificare uno strumento esistente leggi il file intero: sono file lunghi e con logica intrecciata.
- **Non scrivere mai su Trello o su Google Sheet reali durante i test senza il permesso esplicito di Stefano.**

## Strumenti

### estrai_preventivo.html — Estrattore preventivi
- Legge un PDF di preventivo (pdf.js) e ne estrae targa, veicolo, km, manodopera (MDO) e olio.
- Riconosce due layout (`detectTemplate`):
  - **A · Quattroruote/Infocar**: contiene "Preventivo Intervento" e "MDO:".
  - **B · Servizi Auto On Line**.
  - Layout nuovo → aggiungere un `parseTemplateX` e un caso in `detectTemplate`; serve un PDF di esempio.
- Unità di misura riconosciute: NR, LT, HR, KG, PZ, MT (`UM_TOKENS`).
- Scrive su Trello:
  - cerca la scheda per **targa** in tutte le bacheche del workspace (`/search`, `/organizations/{orgId}/boards`);
  - compila i **campi personalizzati** (manodopera, olio) cercandoli per nome sulla bacheca della scheda; tipi non supportati vengono saltati e segnalati;
  - aggiorna la descrizione solo tra i marcatori `<!-- estrattore-preventivi:start -->` e `<!-- estrattore-preventivi:end -->`, senza toccare il resto;
  - può creare una scheda nuova scegliendo ogni volta bacheca e lista; etichetta noleggiatore creata se manca (Ayvens = blu).
- Impostazioni in `localStorage` alla chiave `estrattorePreventivi.trelloSettings`.
- Usato anche da un bookmarklet che apre la pagina con dati precompilati dal portale.

### timbratura.html — Timbratura lavori
- Ore reali degli operai su un lavoro rispetto alla **MDO autorizzata** del preventivo.
- Più operai sullo stesso lavoro (ore sommate), pausa/ripresa, timer live.
- Ogni timbratura è un **commento sulla scheda Trello**: `⏱️ <TIPO> — <operaio>`.
- `localStorage`: `estrattorePreventivi.trelloSettings` (condivisa con l'estrattore), `estrattorePreventivi.operai`, `estrattorePreventivi.timbraturaDevice`.

### ingressi-uscite.html — Presenze dipendenti
- Timbratura ingresso/uscita con **PIN**, verifica **GPS** di vicinanza alla sede e blocco del dispositivo dopo il primo accesso (`salentauto_device_token`).
- Backend: Google Apps Script (`API_URL`), dati su Google Sheet con report mensile ore calcolato da formule sul foglio.
- Azioni API: `timbra`; con `?admin=<chiave>` nell'URL, `set_sede` imposta le coordinate della sede.
- Orario aziendale: 08:30-13:00 / 14:00-17:30, ritardi a slot di 30 minuti.
- Sede: Viale G. Grassi 158, Lecce.

## Trello (workspace "Salentauto Srl")

Bacheche: Officina, Back Office, Pneumatici, Approntamenti, CRM, Gestione Pool.

- **Titolo scheda riparazioni:** `TARGA - MODELLO - NNNNN Km` (es. `GY398RC - Ford Transit - 62689 Km`), targa maiuscola senza spazi.
- **Titolo scheda approntamenti:** `TARGA - TELAIO - MARCA MODELLO`.
- **Descrizione:** NOME COGNOME utilizzatore, telefono, email; riga vuota; lavori come elenco (`- MANUTENZIONE`).
- **Etichette** (gli ID cambiano per bacheca, cercale per nome): noleggiatore (Ayvens, Arval, Alphabet, Privato, NIKO) e tipo (Agenda, Diagnosi, Pool, Carro Soccorso, Carrozzeria, Pneumatici, Sinistro, Sostitutiva, ...).
- **Liste Officina:** DA LAVORARE → IN LAVORAZIONE / LAVORI PROGRAMMATI / PAUSA LAVORO → CONTROLLO FINE LAVORO → INTEGRAZIONI / RICAMBI → CHIUSA DA CONSEGNARE.
- Trello è sul piano gratuito: niente funzioni Premium.

## Idee in sospeso
- Bookmarklet per portare dati dal preventivatore web Quattroruote e dal portale clienti Ayvens/Arval dentro Trello.
- Compilazione dei DDT di consegna (Google Sheet "DDT 2026").
