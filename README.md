# Yobee — AI Operations Hub

Pannello frontend statico per i workflow di automazione AI Yobee. Singola pagina HTML servita via GitHub Pages, ogni card lancia un workflow n8n self-hosted su `n8n.foffyautomazioni.com`.

Pensato per uso interno dei team commerciale, media e network: il commerciale apre la pagina, sceglie il workflow, compila il form e riceve l'output via email.

---

## Live

[https://yobee-stack.github.io/Pannello-automazione/](https://yobee-stack.github.io/Pannello-automazione/)

---

## Workflow disponibili

| # | Workflow | Webhook n8n | Stato |
|---|---|---|---|
| 01 | Cerca nuovi clienti | `/webhook/cerca-clienti` | Placeholder |
| 02 | Gara media + Analisi competitor | `/webhook/gara-media` | Attivo |
| 03 | Scouting editori | `/webhook/scouting-editori` | Placeholder |
| 04 | Email a freddo | `/webhook/cold-email` | Placeholder |
| 05 | Resta aggiornato | `/webhook/resta-aggiornato` | Placeholder |
| 06 | Analisi SEO & GEO | `/webhook/seo-geo` | Placeholder |

Il workflow attivo è il **02 — Gara media**. Gli altri hanno il form configurato sull'hub ma il workflow lato n8n non è ancora costruito.

---

## Workflow 02 — Gara media

Il commerciale fornisce l'URL di un cliente, riceve via email un report HTML con scheda brand e tabella competitor. Architettura two-stage con human-in-the-loop:

```
Hub form  →  Webhook n8n  →  Analisi brand AI  →  Scheda brand via email
                                                      ↓
                                            commerciale APPROVA o MODIFICA
                                                      ↓
                                            Ricerca competitor (Serper)
                                                      ↓
                                            Analisi competitor AI
                                                      ↓
                                            Report finale via email
                                                      ↓
                                            Bottone "Approfondisci social"
                                                      ↓
                                            Apre l'hub in modalità W2
```

### Campi del form

| Campo | Obbligatorio | Descrizione |
|---|---|---|
| Nome richiedente | Sì | Nome del commerciale |
| Email | Sì | Riceverà scheda brand e report finale |
| URL cliente | Sì | Sito da analizzare |
| Cosa sai del brand? | No | Testo libero per fornire contesto qualitativo (settore, posizionamento, peculiarità). Migliora la qualità dell'output. |
| Competitor noti | No | Fino a 5 competitor con nome e URL. Se forniti, il workflow li integra direttamente nell'analisi. |

I campi `contesto` e `competitor_noti` sono opzionali ma il loro utilizzo riduce significativamente errori di identificazione del brand e tempo di esecuzione del workflow.

### Payload inviato al webhook

```json
{
  "nome": "Alfonso Buonaiuto",
  "email": "alfonso.buonaiuto@yobee.it",
  "url": "https://www.esempio.it/",
  "contesto": "Produttore di biscotti per la GDO, posizionamento mid-market.",
  "competitor_noti": [
    { "nome": "Galbusera", "url": "https://www.galbusera.it/" },
    { "nome": "Colussi", "url": "https://www.colussi.it/" }
  ]
}
```

---

## Workflow W2 — Analisi Social Competitor (overlay)

Estensione del workflow 02. Il report finale del workflow Gara media contiene un bottone "Approfondisci con analisi social" che apre l'hub aggiungendo un parametro `?w2=<token>` all'URL.

L'hub:

1. Rileva il parametro `w2` all'apertura
2. Chiama `GET /webhook/w2-session?token=...` per validare il token
3. Se valido, mostra un overlay con la lista competitor identificati dal workflow 02 (checkbox pre-selezionate)
4. Permette di aggiungere competitor non in lista (textarea)
5. Permette di selezionare le piattaforme social da analizzare
6. Submit chiama `POST /webhook/social-competitor` e mostra schermata di conferma

Token monouso, validato lato n8n contro un foglio Google Sheets di sessione.

---

## Struttura del repo

```
Pannello-automazione/
├── index.html        ← unico file: HTML + CSS + JS inline
├── README.md         ← questo file
└── .nojekyll         ← (se presente) disattiva il preprocessing Jekyll
```

Una sola pagina. Nessun build step, nessuna dipendenza npm, nessun framework. CSS variables per il tema Yobee, font da Google Fonts (DM Sans + DM Mono), niente immagini esterne.

---

## Tema visivo

Dark theme ispirato a editorial display tipografico.

| Token | Valore |
|---|---|
| Background | `#070E1A` |
| Accent | `#1D9E75` (verde) |
| Accent light | `#5DCAA5` |
| Font display | DM Sans (300/400/500) |
| Font mono | DM Mono (400/500) |
| Border radius card | 12-16px |
| Grid background | Linee verdi 4% opacity, 48px |

Il pattern delle card workflow è coerente: numero monospace in alto, titolo, descrizione, tag team in basso. Il form attivo si apre sotto la griglia, scroll automatico.

---

## Deploy

Hosting GitHub Pages dalla branch `main`, root del repo.

Per aggiornare la live:

1. Modifica `index.html` su GitHub (tasto matita)
2. Commit direttamente sulla branch `main`
3. GitHub Pages riprocessa in 30-60 secondi
4. Hard refresh sul browser (`Cmd+Shift+R` su Mac, `Ctrl+Shift+R` su Windows) per evitare cache

Non serve nessuna pipeline di build. Modifiche locali con qualunque editor di testo, copia-incolla del file sul GitHub web editor.

---

## Configurazione n8n

L'hub punta a una sola istanza n8n via costante in alto allo `<script>`:

```js
const N8N_BASE = 'https://n8n.foffyautomazioni.com';
```

Se l'istanza cambia (es. dominio, ambiente di staging), modificare solo quella riga.

Tutti i webhook devono essere registrati come webhook di produzione attivi su quell'istanza. Se non lo sono, l'hub mostra un toast di errore "Errore di connessione" al commerciale.

---

## Validazione e gestione errori

Lato hub:

- Validazione dei campi obbligatori: bordo rosso temporaneo sui campi mancanti + toast di errore
- Stato di loading sul bottone Submit durante la richiesta
- Toast verde di conferma su success
- Toast rosso con messaggio diagnostico su errore di rete o HTTP non-2xx

Tutte le validazioni reali sui dati (URL malformati, email duplicate per rate limit, modello AI non disponibile) sono delegate al workflow n8n a valle, che risponde con HTTP appropriati.

---

## Roadmap

Modifiche pianificate non ancora rilasciate:

| Step | Descrizione | Stato |
|---|---|---|
| Refactor MODIFICA Gara media | Sostituire il ramo "MODIFICA" del workflow 02 con un form completo sull'hub (`/modifica?token=...`) che permetta al commerciale di editare nome, settore, posizionamento, punti forza, keyword. | Da progettare |
| Workflow 01 — Cerca clienti | Costruire workflow n8n | Da costruire |
| Workflow 03 — Scouting editori | Costruire workflow n8n | Da costruire |
| Workflow 04 — Cold email | Costruire workflow n8n | Da costruire |
| Workflow 05 — Resta aggiornato | Costruire workflow n8n | Da costruire |
| Workflow 06 — SEO & GEO | Costruire workflow n8n | Da costruire |

---

## Versioning

Versione corrente: **v1.1**

| Versione | Data | Modifiche |
|---|---|---|
| v1.0 | Maggio 2026 | Release iniziale: 6 card workflow, form Gara media con 3 campi base, overlay W2 |
| v1.1 | 16/06/2026 | Form Gara media esteso con campi "Cosa sai del brand?" (textarea) e "Competitor noti" (righe dinamiche, max 5) |

Bumping della versione manuale nel footer di `index.html` a ogni modifica significativa del comportamento.

---

## Contatti

- Repo principale workflow n8n: `yobee-n8n-analysis` (Yobee-stack)
- Maintainer: Alfonso Buonaiuto — `alfonso.buonaiuto@yobee.it`
- Istanza n8n: `https://n8n.foffyautomazioni.com`

---

YOBEE SRL · Via Giacomo Watt 27, Milano
