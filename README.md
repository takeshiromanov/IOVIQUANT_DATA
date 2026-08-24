# IOVIQUANT DATA

Fonte comune, versionata e pubblica per i dataset condivisi dai progetti
IOVIQUANT. Le applicazioni devono leggere questi file in sola lettura e
mantenere un fallback locale per tollerare indisponibilita temporanee di
GitHub.

## Dataset disponibili

| Dataset | File | Aggiornamento |
|---|---|---|
| COR1M daily | `data/cor1m/daily.csv` | automatico, lunedi-venerdi |
| COR1M weekly storico | `data/cor1m/weekly.csv` | manuale |

Il catalogo machine-readable e in `data/catalog.json`.

## URL raw stabili

```text
https://raw.githubusercontent.com/takeshiromanov/IOVIQUANT_DATA/main/data/cor1m/daily.csv
https://raw.githubusercontent.com/takeshiromanov/IOVIQUANT_DATA/main/data/cor1m/weekly.csv
https://raw.githubusercontent.com/takeshiromanov/IOVIQUANT_DATA/main/data/catalog.json
```

## Aggiornamento COR1M

Il workflow `.github/workflows/update-cor1m.yml` gira alle 23:35
Europe/Rome dal lunedi al venerdi ed e avviabile anche manualmente. Recupera
l'ultima osservazione di `^COR1M` tramite `yfinance` e usa la data della
quotazione restituita:

- inserisce la data se assente;
- aggiorna la riga se gia presente;
- imposta `Price`, `Open`, `High` e `Low` al valore `Close` ricevuto;
- ignora i weekend nel controllo del ritardo;
- non crea commit se il file e gia aggiornato.

Il weekly conserva la storia precedente all'inizio della serie daily e non
viene alterato dal job.

## Contratto per le app consumer

1. Leggere prima l'URL raw centrale.
2. Validare almeno `Date`, `Price`, `Open`, `High`, `Low`.
3. Usare una cache con TTL limitato (consigliato: 1-6 ore).
4. In caso di errore di rete o schema, ripiegare sulla copia locale.
5. Segnalare se l'ultima data valida e in ritardo di oltre due giorni feriali.

I nuovi dataset comuni vanno aggiunti sotto `data/<dataset>/` e registrati in
`data/catalog.json`; ciascun updater deve avere test e workflow dedicati.
