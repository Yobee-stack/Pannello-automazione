# Yobee AI Operations Hub

Questa repository contiene la dashboard (interfaccia utente web) per avviare i workflow n8n di Yobee.

## Struttura
- `index.html`: La dashboard statica. Include tutti i form e la logica per comunicare con i webhook di n8n.
- Nessun server backend richiesto (l'interfaccia comunica direttamente con `n8n.foffyautomazioni.com`).

## Workflow Integrati
1. **Cerca nuovi clienti**: Prospecting basato su settore e area.
2. **Gara media + Analisi competitor**: Workflow avanzato (W1) per l'analisi del brand, generazione keyword e identificazione competitor. Ora include campi obbligatori di contesto (Modello di Business, Settore) per guidare l'Intelligenza Artificiale. Include anche il form dinamico (W2) per l'analisi dei profili social.
3. **Scouting editori**: Ricerca publisher per il network.
4. **Email a freddo**: Generazione bozze personalizzate.
5. **Resta aggiornato**: Monitoraggio fonti e digest.
6. **Analisi SEO & GEO**: Analisi per motori classici e AI search.

## Aggiornamenti Recenti
* **v1.2**: Migliorato il form "Gara media" introducendo i campi obbligatori "Modello di Business" (Menu a tendina: B2C, B2B, B2B2C) e "Settore specifico". Il campo testuale di contesto è stato rinominato in "Cosa sai del brand?" e reso obbligatorio. Questo aggiornamento serve a fornire un contesto preciso all'AI, evitando errori di filtraggio competitor e migliorando drasticamente la precisione della ricerca. I dati vengono combinati in un singolo blocco e passati al webhook.
