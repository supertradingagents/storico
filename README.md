# Storico verificabile di STA · STA verifiable history

Questo repository è lo storico pubblico delle schede di **[STA: Super Trading Agents](https://www.supertradingagents.com/it/)**,
il servizio di segnali di trading per cripto e azioni USA su OKX costruito da dieci agenti di intelligenza artificiale.
Nessuno lo può ritoccare senza che si veda, nemmeno noi.

Sito: https://www.supertradingagents.com · Come funziona: https://www.supertradingagents.com/it/come-funziona.html

## Cosa c'è

- `impronte/AAAA/MM/AAAA-MM-GG.txt`: le impronte (SHA-256) delle schede pubblicate quel giorno (UTC), una per riga:
  `impronta  ora di nascita (UTC)  id della scheda`. Si aggiungono ogni ora.
- `impronte/…/AAAA-MM-GG.txt.ots`: la prova OpenTimestamps del file, che lo lega a un blocco di Bitcoin. La si mette
  quando il giorno è chiuso: da lì il file non cambia più.
- `schede/AAAA/MM/AAAA-MM-GG.jsonl`: i dati delle schede, pubblicati 48 ore dopo la loro fine (stop o target toccati,
  oppure 168 ore di osservazione), perdite comprese, con l'esito sulla carta.

## Come si verifica

L'impronta di una scheda è lo SHA-256 di questo testo, senza spazi né a capo:

    v1|<id>|<moneta>|<LONG o SHORT>|<entry>|<stop>|<target>|<nata, AAAA-MM-GGTHH:MM:SSZ>

I numeri sono scritti con il punto e senza zeri finali (148.2, non 148.20). Esempio in Python:

    import hashlib
    testo = "v1|<id>|SOL-USDC|LONG|148.2|143.9|156.8|2026-10-17T14:12:00Z"
    print(hashlib.sha256(testo.encode()).hexdigest())

Ogni riga di `schede/` ha anche il campo `testo`: ricalcola l'impronta e cercala in `impronte/` nel file del giorno
in cui la scheda è nata (o il successivo). La prova di Bitcoin si controlla con il client OpenTimestamps:
`ots verify impronte/AAAA/MM/AAAA-MM-GG.txt.ots`.

Le schede sono informazioni generali, non consulenza. Gli esiti «sulla carta» misurano entry, stop e target della
scheda, non il risultato di un conto.

---

This repository is the public history of the trade cards of **[STA: Super Trading Agents](https://www.supertradingagents.com/en/)**,
AI trading signals for crypto and US stocks on OKX (website: https://www.supertradingagents.com). `impronte/` holds the SHA-256 fingerprint of every card,
added hourly and anchored to Bitcoin every day with OpenTimestamps (`.ots`). `schede/` holds the card data, published
48 hours after each card ends, losses included. The fingerprint is SHA-256 of
`v1|<id>|<pair>|<LONG or SHORT>|<entry>|<stop>|<target>|<born, YYYY-MM-DDTHH:MM:SSZ>` (dot decimals, no trailing
zeros).
