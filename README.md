# phish-feed

A continuously updated feed of **currently live phishing hosts**, found and
checked by an independent detection pipeline that I run on my own. I publish it
as an ingest source for anti-phishing blocklists, DNS resolvers and wallet
protection services. It is not affiliated with any of them.

This is a **current-state** list, not an archive. Each run republishes every
host that is live right now. A host drops off automatically when its page goes
dead or I withdraw the detection, so a consumer that replaces its copy on each
pull never has to process removals.

## Feeds

| Slice | Plain hosts | With metadata | For |
|---|---|---|---|
| all | [`phishfeed_all.hosts`](phishfeed_all.hosts) | [`phishfeed_all.json`](phishfeed_all.json) | URL and host blocklists |
| crypto | [`phishfeed_crypto.hosts`](phishfeed_crypto.hosts) | [`phishfeed_crypto.json`](phishfeed_crypto.json) | wallet and crypto-scoped blocklists |
| dns | [`phishfeed_dns.hosts`](phishfeed_dns.hosts) | [`phishfeed_dns.json`](phishfeed_dns.json) | DNS resolvers that block a whole name |

Raw ingest URLs:

```
https://raw.githubusercontent.com/jaym2026/phish-feed/main/phishfeed_all.hosts
https://raw.githubusercontent.com/jaym2026/phish-feed/main/phishfeed_crypto.hosts
https://raw.githubusercontent.com/jaym2026/phish-feed/main/phishfeed_dns.hosts
```

The `.hosts` files carry one hostname per line. Each `.json` file carries
`generated_at` (UTC) and `count` at the top, then one object per host in
`hosts`.

## What qualifies

**all**: the host serves a live phishing page, confirmed either by my own
check of the page or by at least two independent outside sources. Before
anything is listed, these are removed:
- tutorial, portfolio and coursework clones
- sub-resources (images, scripts) that are not pages
- any shared-platform hostname where the abuse is only in the path, such as a
  shortener, a paste site or an object-storage endpoint. Listing those would
  block the whole provider.

**crypto**: the subset whose brand or hostname matches a wallet, exchange or
DeFi term.

**dns**: the subset that is safe to block at the resolver. A URL blocklist
stops one page, but a resolver stops the whole name for every user behind it.
So this slice holds only names that exist to serve the phishing page. Each
entry's `dns_reason` says which of these three it is:

| `dns_reason` | Meaning |
|---|---|
| `platform_tenant` | a hostname that is one tenant's deployment on a shared platform (for example `name.pages.dev`), so blocking it affects nobody else |
| `young_domain` | a domain registered within the last 180 days |
| `root_kit` | the phishing page is the site itself, served at the root of a domain not known to be old |

A compromised legitimate site with a kit dropped in a folder is never in this
slice, because blocking the name would take the victim's own site offline. It
stays in **all** for URL-level blocklists.

The dns slice also applies a stricter evidence bar. Every entry needs all of
the following:
- my own check of the page (outside listings alone are not enough)
- a named impersonated brand, or a listing by an independent phishing feed
- a liveness check within the last 48 hours
- a page that is not a stock hosting control panel, a suspension notice or a
  server default page

## Fields (JSON)

| Field | Meaning |
|---|---|
| `host` | the hostname to block |
| `category` | `phishing`, `malware` or `command_and_control` |
| `brand` | the impersonated brand, when known (`brands` lists all of them when there are several) |
| `crypto` | true when the entry is in the crypto slice |
| `dns_reason` | why a resolver may block the name (see above) |
| `evidence_url` | the most recent URL seen serving the page on this host |
| `hosting_platform` | the hosting provider, when recognized |
| `verified` | true when my own check confirmed the phishing page |
| `corroboration_count` | how many independent outside sources also flag it |
| `source_feed` | the channel through which the host was first discovered |
| `first_seen` / `last_seen` | UTC, `YYYY-MM-DD HH:MM:SS` |
| `last_checked` | UTC time of the most recent liveness check |

## False positives

If a host here should not be, please open an issue. This link opens one with
the title ready to edit (replace `example.com` with the hostname):

```
https://github.com/jaym2026/phish-feed/issues/new?title=False%20positive:%20example.com
```

I review reports and remove a confirmed false positive. Because the feed is
current-state, the host is gone from the next hourly publish, so consumers do
not need a separate retraction.

## Cadence

Republished automatically every hour. Every JSON file carries `generated_at`.
If the feed stops updating, a watchdog alerts me after 48 hours.

## Contact

Open an issue: <https://github.com/jaym2026/phish-feed/issues>

## License

Data is published for defensive use. No warranty: verify before enforcement.
