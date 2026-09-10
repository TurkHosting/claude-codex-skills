---
name: ptr-abuse-check
description: >
  Analyse a customer's PTR (reverse DNS) request for phishing, brand-impersonation
  and spam abuse signals BEFORE any PTR record is written, and report the findings
  to the operator. Advisory only — it never approves, refuses or applies anything
  by itself; the operator always makes the final call. Use ONLY when the user
  explicitly invokes it (/ptr-abuse-check) with one or more "IP -> requested PTR"
  pairs. On request it also produces a ready-to-send customer reply asking for
  authorization or an alternative domain.
disable-model-invocation: true
---

# PTR abuse check

Screens PTR update requests coming from customers. The single question this skill
answers is: **does the requested hostname look like an attempt to impersonate
someone else?**

## Hard rules

1. **Analysis only.** NEVER call `update-ptr-records-tool`, `reset-ptr-records-tool`
   or any other write tool from this skill. Read-only lookups
   (`query-ptr-records-tool`, `query-ip-info-tool`, `dig`/`host`) are fine.
2. **Never decide.** Do not say "this request is rejected", "we should refuse" or
   "I applied it". Report signals and a risk level, then stop. Applying the PTR is
   a separate, explicit operator instruction — and when it comes, it runs through
   the normal tool flow (4-digit PIN as the last step), not through this skill.
3. **Never lecture the operator.** They know the business. Give the evidence
   compactly; no warnings about ethics, no repeated caveats.
4. **Only send the customer reply when asked.** Produce the template only if the
   operator asks for it ("şablon ver", "yanıt hazırla", "template"). A risk report
   alone must not drag a letter behind it.
5. **Uncertainty is reported as uncertainty.** If the evidence is thin, say so
   instead of inflating it into a red flag. False alarms cost the operator more
   than they save.

## Input

Accepts whatever the operator pastes: a raw customer message, a list of
`IP -> hostname` lines, any separator (`=`, `=>`, `->`, `:`), or a single pair.
Extract every `(IP, requested PTR)` pair. If an IP or a hostname is missing, ask
for it — do not guess.

## Analysis steps

Run these for all pairs in one batch (each tool accepts up to 40 IPs per call).

### 1. Registrable domain

Reduce the requested hostname to its registrable domain (eTLD+1), honouring
multi-label suffixes:

- `www.yana.dhl.com` → `dhl.com`
- `mail.musteri.com.tr` → `musteri.com.tr`
- `dhl.musteridomain.com` → `musteridomain.com` (brand is only a subdomain)

The registrable domain is what the check is about. A brand name appearing only in
a subdomain of a domain the customer owns is a low-severity note, not a red flag.

### 2. Watchlist match

Compare the registrable domain against `watchlist.md` (global brands, TR banks /
cargo / telecom, restricted suffixes, large mail providers, our own infrastructure
domains). A match is a signal that requires proof of control — **not** an automatic
verdict.

### 3. Look-alike and campaign patterns

- Homoglyph / typosquat: `dhI.com`, `paypa1.com`, `micros0ft.com`
- Punycode / IDN: any `xn--` label
- Brand + affix: `dhl-tracking.com`, `secure-paypal-login.net`, `dhl-cargo.info`
- Phishing vocabulary next to a brand: login, secure, verify, account, update,
  billing, webmail, support
- Prize / campaign vocabulary next to a brand — the dominant pattern in
  brand-abuse hostnames: `concorso`, `premio`, `promo`, `sorteo`, `gewinnspiel`,
  `win`, `prize`, `reward`, `bonus`, `gift`, `giftcard`, `iphone`, `cekilis`
- Parcel / customs vocabulary — the second dominant pattern: `tracking`,
  `parcel`, `delivery`, `shipment`, `customs`, `kargo`, `gumruk`, `takip`
- Brand name on a cheap, heavily abused TLD: `.top`, `.xyz`, `.icu`, `.cyou`,
  `.click`, `.online`, `.shop`, `.live`, `.info`
- Full lexicon in `watchlist.md`

### 4. Proof of control (necessary, but NOT sufficient)

Resolve the requested hostname's A/AAAA record and compare it with the IP:

- **A record points elsewhere / does not exist** → no proof of control. This is
  the strongest negative signal in the whole check.
- **A record points at this IP** → normally evidence that whoever asked controls
  that domain's DNS. Report it — but only after running 4a and 4b below, because
  an A-record match is trivially satisfied by the two structures described there.

`validate_a_record: true` on the write tool performs exactly this comparison, so
it inherits the same blind spots. Never present it to the operator as a guarantee.

#### 4a. Fan-out / fast-flux

Count the A records the hostname resolves to and where they live:

- More than ~5 addresses spread across unrelated networks, ASNs or countries is
  **fast-flux hosting**, not a CDN. A real CDN answers with a few addresses from
  one operator's ranges.
- In that structure our IP is merely one node of a rotating pool, so the
  A-record match proves nothing about who is asking. **The fan-out finding
  overrides a passing proof-of-control check.**

Observed case: `concorsotimvision.tim.it` resolved to 57 addresses across dozens
of unrelated networks, one of which was the IP in the request — a formally
passing FCrDNS check on a prize-scam hostname.

#### 4b. CNAME chain

Follow the full chain, not just the final address. A hostname under a trusted
brand that CNAMEs to a different registrable domain
(`concorsotimvision.tim.it → www.concorsotimvision.it`) inherits the trust of the
brand while the content lives elsewhere. Classify the **final target** too, and
report the chain explicitly.

#### 4c. Domain age

Where whois is available, note the registration date. A domain younger than ~90
days carrying a brand-adjacent name is a strong abuse signal; abuse
infrastructure is rotated far faster than legitimate mail infrastructure.

Also query the current PTR (`query-ptr-records-tool`) and the IP's owner
(`query-ip-info-tool`) so the operator sees who is actually asking.

### 5. Purpose plausibility

The legitimate reason for a custom PTR is outbound mail deliverability (FCrDNS),
so the hostname is normally a mail/host name: `mail.`, `mx1.`, `smtp.`, `srv1.`,
`ns1.`, aligned with the server's HELO/EHLO. A `www.` hostname as a PTR target is
technically pointless — a PTR does not make a website reachable, load faster or
rank better, so a `www.` request means the customer wants the IP to *look like*
that site. Note it.

Malformed or generic targets are invalid regardless of who asks, and are
themselves listing criteria for Spamhaus CSS: `localhost.localdomain`, a bare IP
literal, `WIN-XXXXXXX`, unqualified single-label names, or anything that is not
an FQDN.

### 6. Pattern across the request and over time

- Several unrelated third-party brands from one customer, or many freshly
  registered look-alike domains in one batch, is a bulk-abuse (snowshoe spam)
  signal. Mention it only when there is more than one pair.
- Check `query-ip-usage-logs-tool` for churn: the same IP renamed repeatedly over
  a short period, or a customer cycling PTRs across a block of IPs, matches the
  snowshoe profile Spamhaus CSS targets — low volume per IP, many rotating names.

## Risk levels

Three levels, reported to the operator with the Turkish labels shown in brackets.

| Level | Meaning |
|---|---|
| 🔴 High (`Yüksek`) | Registrable domain belongs to a third party or a restricted suffix (`.gov`, `.gov.tr`, `.edu.tr`, `.mil.tr`), or is a clear look-alike — **and** control is not proven. Also high whenever fast-flux fan-out, an off-domain CNAME under a brand, or an invalid PTR target is found, **even if the A record matches**. |
| 🟡 Suspicious (`Şüpheli`) | Signals present but not conclusive: look-alike without a clear target, abuse-lexicon or abused-TLD hit, unresolvable hostname, freshly registered domain, PTR churn on the IP, purpose does not fit. |
| 🟢 Clean (`Temiz`) | Customer's own domain, standard host naming, or a watchlist match where the A record points at this IP **and** steps 4a–4c found nothing. |

## Output to the operator

The operator reads Turkish, so **write the report in Turkish** even though these
instructions are in English. Keep it short: one table, the evidence lines that
matter, then a single closing line stating that the decision is theirs.

Layout to follow (Turkish labels are fixed; values are filled from the analysis):

```
PTR risk analizi

| IP | İstenen PTR | Kayıtlı alan adı | Risk | Sinyal |
|----|-------------|------------------|------|--------|
| 95.173.180.104 | www.yana.dhl.com | dhl.com | 🔴 Yüksek | Üçüncü taraf markası; A kaydı bu IP'yi göstermiyor |

Detay
- dhl.com A kaydı → 104.18.x.x (bu IP değil) → domain kontrolü kanıtlanmıyor
- Mevcut PTR: static-180-104.netmax.com.tr
- IP sahibi: <user_name> (kayıtlarda)
- `www.` hostname'i PTR hedefi olarak teknik bir işe yaramıyor

Karar sende. İstersen müşteriye gönderilecek yanıt şablonunu hazırlayayım.
```

For a clean request two lines are enough: the risk level and a note that no
suspicious signal was found.

## Customer reply

Only when the operator asks for it ("şablon ver", "yanıt hazırla", "template").
Use `response-templates.md` verbatim, filling `{IP}`, `{REQUESTED_PTR}` and
`{BRAND_DOMAIN}`. The wording is the operator's own approved text — do not
rewrite, soften or "improve" it. Each template exists in an English and a Turkish
version; default to English and use the Turkish one when the customer wrote in
Turkish.

If the operator wants the technical-proof variant instead (asking the customer to
create an A record rather than to produce written authorization), use the
"Proof of control" template in the same file.
