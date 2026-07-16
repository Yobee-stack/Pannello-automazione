# Aggiornamento README — blocchi da incollare

## 1. Riga di changelog (repo `Yobee-stack/workflows`, README di progetto `scraping-editori`)

Da aggiungere in cima alla sezione changelog:

```markdown
### 2026-07-16 — Selettore mercato + scouting geografico (nodo 7 v2)
- Analizzato feedback team (304 siti scaricati, ~10% contattabili, solo 2% italiani).
  Cause identificate: pool query esaurito dopo pochi lanci, asimmetria strutturale
  IT/ES (blacklist + requisito ads.txt + anti-portfolio solo lato Italia),
  campo note mai collegato alla pipeline.
- Nodo 7 riscritto (v2): nuovo campo form `mercato` (IT / ES / ENTRAMBI, default
  ENTRAMBI retrocompatibile). Con "Solo Italia" genera 10 query IT invece di 5+5.
- Query IT ora per metà tematiche (pool v1) e per metà geografiche, con rotazione
  pseudo-casuale su ~60 città/province italiane (escluse Roma e Milano). Il pool
  diventa di fatto inesauribile e punta agli editori locali non ancora contattati.
- Il campo note ora viene letto dal nodo 7: se contiene una città o regione
  italiana riconosciuta (match a parola intera), le query geografiche si ancorano
  lì. "Italia/Italy" nelle note resta ignorato: si usa il selettore mercato.
- Pannello aggiornato a v1.5: selettore "Mercato" nella card 03, campo `mercato`
  nel payload del webhook, placeholder note riscritto per guidare l'uso geografico.
- Interventi pianificati a seguire: breakdown del funnel scarti nel report (D),
  filtro traffico 70k pre-report via Apify vortex_data con sezione "sotto soglia" (C),
  pertinenza verticale come flag AI (E). Aperto anche il caso "9 domini su 13"
  nell'analisi traffico del 15/07: in attesa di input/dataset del run Apify.
```

## 2. Aggiornamento sezione "Stato" del README di progetto

Sostituire la descrizione dello stato del Workflow 03 con:

```markdown
**Workflow 03 — Scouting Editori IT+ES**
- Parte 1 operativa. Nodo 7 in versione v2 (selettore mercato + rotazione
  geografica su città italiane + lettura geo dalle note).
- Parte 2 (Analisi Traffico via Apify) operativa con pattern link monouso w3.
- Form pannello: nome, email, verticale, formato, mercato (IT/ES/ENTRAMBI),
  numero_editori (max 20), note (geo o esclusioni).
```

## 3. Repo `Yobee-stack/Pannello-automazione` — messaggio di commit

Per il commit dell'`index.html`:

```
v1.5 — card 03: selettore Mercato (IT/ES/ENTRAMBI), campo mercato nel
payload webhook, placeholder note orientato a città/regione italiana
```

Se il repo del pannello ha un proprio README con changelog, aggiungere:

```markdown
### v1.5 — 2026-07-16
- Card 03 Scouting editori: nuovo selettore "Mercato" (Italia + Spagna /
  Solo Italia / Solo Spagna, default Italia + Spagna).
- Il payload del webhook include il nuovo campo `mercato`.
- Placeholder del campo note aggiornato: suggerisce di indicare una città
  o regione italiana per orientare la ricerca geografica.
```

## Regole operative (invariate, promemoria)

- Mai reimportare il JSON in n8n senza riselezionare manualmente le tab
  Google Sheets nei nodi GSheets (i gid non sopravvivono all'import).
- Il nodo 7 v2 va incollato direttamente nel Code node in n8n, NON via
  reimport del JSON. Dopo la modifica: export → rinomina
  `03_Scouting_Editori_IT_ES_v4_2026-07-16.json` → upload nel repo.
