# Yobee AI Operations Hub

Sito unico su GitHub Pages, servito da `main`: https://yobee-stack.github.io/Pannello-automazione/

- `/` — Automazioni (colleghi): avvia i workflow n8n dalle card.
- `/contatore/` — I tuoi lanci (colleghi): ogni collega inserisce il proprio numero identificativo e vede quanti lanci gli restano nel mese.
- `/admin/` — Statistiche aggregate (solo Alfonso): riepilogo per workflow/utente/credenziale ed eventi. Protetto da Basic Auth sul nodo Webhook `contatore-stats` del workflow n8n **Contatore — Endpoint Stats (monitoraggio admin)** (cartella "Contatore lanci").
  Attenzione: la protezione vive interamente su n8n. Il login della pagina raccoglie le credenziali e le invia, ma non verifica nulla da solo: se il webhook risponde 200 senza Basic Auth, la pagina mostra i dati a chiunque e il login accetta qualsiasi password.
  Verifica (da rifare dopo ogni modifica al workflow): apri in finestra anonima `https://n8n.foffyautomazioni.com/webhook/contatore-stats`. Deve chiedere utente e password. Se invece restituisce JSON, la protezione non è attiva.

I repo `Contatore--Admin` e `contatore-workflow` sono stati dismessi in favore di questo hub: le loro GitHub Pages ora reindirizzano qui.

## Endpoint n8n usati dalle pagine

| Endpoint | Workflow n8n | Risposta | Usato da |
| --- | --- | --- | --- |
| `quota` | Contatore — Endpoint Quota | `{trovato, numero}` | `/contatore/` |
| `quota-status` | 03 — Scouting Editori IT ES v6d (nodo "Webhook — quota-status", GET) | `{meseAnno, quotaMax, richiesteMese, richiesteRimanenti}` | pill quota sulla card Scouting editori in `/` |
| `contatore-stats` | Contatore — Endpoint Stats (monitoraggio admin) | statistiche aggregate — **protetto da Basic Auth** | `/admin/` |

Base: `https://n8n.foffyautomazioni.com/webhook/`. `quota-status` non sta nel workflow del contatore: vive dentro lo scouting editori perche legge lo stesso foglio quota di quel workflow.
