[Email Validator](https://apify.com/accurate_pouch/email-validator?fpr=data)

Validate 10,000 emails in minutes. SMTP mailbox check, disposable domain detection, catch-all flagging, role-based detection, deliverability scoring, and typo suggestions — all in one actor.

---

## Why this exists

The most popular email validator on Apify has 743 users but only a 3.3/5 rating. The second most popular has 142 users and a 2.0/5 rating. Users are stuck choosing between unreliable tools and overpriced ones.

This actor does everything the existing ones do, plus:

- **Disposable domain detection** — flags mailinator, guerrillamail, and 5,300+ throwaway providers
- **Catch-all detection** — identifies servers that accept all addresses (less trustworthy)
- **Role-based detection** — flags info@, admin@, support@ addresses
- **Deliverability score** — 0-100 composite score based on all checks
- **Typo suggestions** — catches gmial.com, hotmal.com, and 20+ common misspellings
- **20 emails** — test it before committing

---

## Quick start

```
{
    "emails": [
        "real-user@gmail.com",
        "fake@mailinator.com",
        "info@example.com"
    ],
    "checkSmtp": true,
    "checkDisposable": true
}
```

---

## Competitor comparison

| Feature | xmiso (743 users, 3.3★) | api-ninja (204 users, 5★) | **This actor** |
| --- | --- | --- | --- |
| SMTP mailbox check | Unknown | Yes | Yes |
| MX record check | Unknown | Yes | Yes |
| Disposable detection | No | No | Yes (200+ domains) |
| Catch-all detection | No | No | Yes |
| Role-based detection | No | No | Yes |
| Free provider flagging | No | No | Yes |
| Deliverability score | No | No | Yes (0-100) |
| Typo suggestions | No | No | Yes |
| Per-email error detail | No | Limited | Yes |
| Dry run mode | No | No | Yes |
| Free tier | No | Limited | **20 emails free** |
| Price per email | $0.0025 | $0.0025 | $0.003 |

---

## Input

| Field | Type | Default | Description |
| --- | --- | --- | --- |
| `emails` | array | *(optional)* | List of email addresses to validate. Or use `emailsUrl`. |
| `emailsUrl` | string | *(optional)* | URL to CSV or JSON file with emails. One per line, or JSON array. For 10K+ lists. |
| `checkSmtp` | boolean | `true` | Connect to mail server and verify mailbox exists |
| `checkDisposable` | boolean | `true` | Check against 5,300+ disposable domain list |
| `timeout` | integer | `10000` | SMTP timeout in ms (3,000–30,000) |
| `maxConcurrency` | integer | `5` | Parallel validations (1–20). Keep low to avoid mail server blocks |
| `googleSheetsId` | string | *(optional)* | Export results to this Google Sheet (spreadsheet ID) |
| `googleServiceAccountKey` | string | *(optional)* | Google Service Account JSON key for Sheets export |
| `dryRun` | boolean | `false` | Preview results without charges |

---

## Output

Each email produces one item in the dataset:

```
{
    "email": "user@gmail.com",
    "valid": true,
    "deliverable": true,
    "disposable": false,
    "catchAll": false,
    "roleBased": false,
    "freeProvider": true,
    "deliverabilityScore": 90,
    "checks": {
        "syntax": "pass",
        "domain": "pass",
        "mxRecord": "pass",
        "smtp": "pass"
    },
    "suggestion": null,
    "smtpDetail": "gmail.com is a major provider that blocks SMTP verification probes. Email syntax, domain, and MX are valid — deliverability cannot be confirmed via SMTP for this provider.",
    "error": null
}
```

| Field | Description |
| --- | --- |
| `email` | Normalised email (lowercased, trimmed) |
| `valid` | Syntax + domain + MX all pass |
| `deliverable` | SMTP check confirms mailbox exists (`null` if SMTP skipped or inconclusive) |
| `disposable` | Domain is a known throwaway provider |
| `catchAll` | Mail server accepts all addresses (less trustworthy) |
| `roleBased` | Address is a generic role (info@, admin@, support@) |
| `freeProvider` | Domain is a free email provider (gmail, yahoo, etc) |
| `deliverabilityScore` | 0-100 composite score across all checks |
| `checks` | Per-check status: `pass`, `fail`, `unknown`, or `skip` |
| `suggestion` | Typo correction if detected (e.g., gmial.com → gmail.com) |
| `smtpDetail` | Plain English explanation of the SMTP result — why it passed, failed, or was inconclusive |
| `error` | Error message if a check failed, otherwise `null` |

### Deliverability score breakdown

| Check | Points |
| --- | --- |
| Syntax valid | +20 |
| Domain exists | +20 |
| MX records found | +20 |
| SMTP confirms mailbox | +30 (unknown = +15) |
| Not disposable | +5 |
| Not role-based | +5 |
| Catch-all server | -10 |

---

## How SMTP validation works

1. Connect to the domain's mail server (MX record, port 25)
2. Send `EHLO` greeting
3. Send `MAIL FROM` with a verification address
4. Send `RCPT TO` with the target email
5. Read the response — `250` means the mailbox exists, `550` means it doesn't
6. Disconnect without sending any email

No email is ever sent. This is standard practice used by every email validation service.

---

## Pricing

**$0.003 per email validated** (pay-per-event pricing).

- Charged per email processed, regardless of valid/invalid result.
- Errors and dry runs are never charged.
- 1,000 emails = $3.00
- 10,000 emails = $30.00

---

## Limitations

- **SMTP checks are blocked by major providers.** Gmail, Outlook, Yahoo, and other large providers block SMTP verification probes. For these domains, SMTP returns `unknown` and the `smtpDetail` field explains why. Syntax, domain, and MX checks still run — deliverability just can't be confirmed via SMTP for those providers.
- **Catch-all servers always return 250.** If a server accepts all addresses, the SMTP check will say "deliverable" even for nonexistent mailboxes. The `catchAll` flag warns you about this.
- **Not real-time delivery guarantee.** A "deliverable" result means the mailbox existed at check time. The inbox could be full, disabled, or deleted minutes later.
- **Disposable domain list covers 5,300+ providers** from the canonical open-source list. New throwaway services appear constantly — coverage is good but not 100%.
- **Rate limiting.** Keep `maxConcurrency` at 5 or below for best results. Higher values may trigger temporary blocks from mail servers.

---

## Related Tools by manchittlab

- **[Broken Link Checker](https://apify.com/accurate_pouch/broken-link-checker)** — Recursively crawl your website and find every broken link, 404, redirect, and timeout.
- **[Domain Age Checker](https://apify.com/accurate_pouch/domain-age-checker)** — Bulk RDAP domain age, registration, and expiration lookup.
- **[Lighthouse Auditor](https://apify.com/accurate_pouch/lighthouse-auditor)** — Batch Lighthouse audits for performance, SEO, accessibility, and Core Web Vitals.
- **[Image Processor](https://apify.com/accurate_pouch/image-processor)** — Batch resize, convert to WebP/AVIF, compress, watermark. Powered by sharp.
- **[Tech Stack Detector](https://apify.com/accurate_pouch/tech-stack-detector)** — Detect frameworks, CMS, analytics, CDN, and 100+ technologies for any URL.
- **[Google Sheets Reader & Writer](https://apify.com/accurate_pouch/google-sheets-rw)** — Read any Google Sheet to JSON or append rows. Service Account auth.

---

## Run on Apify

[![Run on Apify](https://images.apifyusercontent.com/gTvnlJwwVJ1NebWERlWLuTx2RlOJmA4EocV-NvWtCY4/w:1800/cb:1/aHR0cHM6Ly9hcGlmeS5jb20vc3RhdGljL3J1bi1vbi1hcGlmeS5zdmc.webp)](https://apify.com/accurate_pouch/email-validator)

No setup needed. Click above to run in the cloud. $0.003 per operation.