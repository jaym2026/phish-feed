# phish-feed

A continuously-updated feed of **currently-live phishing hosts** verified by an
independent detection pipeline (CT-log + cloud/bucket hunters + own content
verifier + external corroboration). Intended as an **ingest source** for
anti-phishing blocklists and wallet-protection networks (e.g. SEAL /
PhishDestroy → MetaMask / Phantom).

Every host listed here was confirmed **live** at publish time. Hosts drop off
automatically once they go dead, so the list reflects the present threat surface
rather than a historical archive.

## Feeds

| File | Format | Contents |
|---|---|---|
| [`phishfeed_all.hosts`](phishfeed_all.hosts) | host per line | all live verified phishing hosts |
| [`phishfeed_crypto.hosts`](phishfeed_crypto.hosts) | host per line | crypto/wallet-impersonation subset |
| [`phishfeed_all.json`](phishfeed_all.json) | JSON | same hosts + `first_seen`, `last_seen`, `target_brand`, `evidence_url` |
| [`phishfeed_crypto.json`](phishfeed_crypto.json) | JSON | crypto subset with metadata |

Raw ingest URLs:

```
https://raw.githubusercontent.com/jaym2026/phish-feed/main/phishfeed_all.hosts
https://raw.githubusercontent.com/jaym2026/phish-feed/main/phishfeed_crypto.hosts
```

The JSON carries a top-level `generated_at` (UTC) so pullers can gauge freshness.

## Confidence

Hosts are gated on the detector's high-confidence verdict (`verified_phish`) and
pass a tutorial/portfolio false-positive filter before listing. Corroboration
and evidence URLs are available in the JSON. Mistakes happen — open an issue and
we'll pull a false positive fast.

## Cadence

Republished automatically (typically hourly). If a feed looks stale, the
producer is alerted by an out-of-band 48-hour freshness watchdog.

## Contact

Open an issue: <https://github.com/jaym2026/phish-feed/issues>

## License

Data is published for defensive use. No warranty; verify before enforcement.
