[Email Validator](https://apify.com/junipr/email-validator?fpr=data)

# Email Validator

## What does Email Validator do?

Email Validator performs multi-layer validation on any list of email addresses to determine if they are real, deliverable, and safe to use. It checks email format against RFC 5322, verifies the domain has valid MX DNS records, connects to the mail server via SMTP to confirm the mailbox exists, detects disposable/temporary email providers (30,000+ domains), flags role-based addresses (admin@, info@, support@), identifies free email providers (Gmail, Yahoo, Outlook), and optionally detects catch-all domains.

Each email receives a quality score from 0 to 100 and a human-readable reason explaining the result. The actor also suggests corrections for common domain typos (e.g., `gmial.com` to `gmail.com`). Use it to clean email lists before outreach campaigns, validate sign-up forms, or enrich contact data with deliverability intelligence.

## Features

- **Format validation** — RFC 5322 compliant email format checking
- **MX record verification** — Confirms the domain has valid mail exchange DNS records
- **SMTP mailbox verification** — Connects to the mail server and checks if the specific mailbox exists using RCPT TO
- **Disposable domain detection** — Checks against 30,000+ known disposable and temporary email providers
- **Role-based address detection** — Flags addresses like admin@, info@, support@, noreply@, sales@, webmaster@
- **Free provider detection** — Identifies Gmail, Yahoo, Hotmail, Outlook, and other free email services
- **Catch-all detection** — Optionally checks if the domain accepts all email addresses regardless of the local part
- **Typo suggestions** — Detects common domain misspellings and suggests corrections (gmial.com, yaho.com, outloo.com)
- **Quality scoring** — 0-100 score based on format, MX, SMTP, disposable status, role status, and provider type
- **Batch processing** — Validate hundreds of emails concurrently with configurable parallelism
- **Automatic deduplication** — Duplicate emails are removed before processing

## Input Configuration

```
{
  "emails": ["john@company.com", "test@gmail.com", "fake@temp-mail.io"],
  "checkMx": true,
  "checkSmtp": true,
  "checkDisposable": true,
  "checkRole": true,
  "checkFreeProvider": true,
  "checkCatchAll": false,
  "smtpTimeout": 10000,
  "maxConcurrency": 5
}
```

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `emails` | string[] | `["test@gmail.com", "invalid@nonexistent-domain-12345.com"]` | Email addresses to validate |
| `checkMx` | boolean | `true` | Verify domain has MX DNS records |
| `checkSmtp` | boolean | `true` | Verify mailbox exists via SMTP RCPT TO |
| `checkDisposable` | boolean | `true` | Check against 30,000+ disposable email domains |
| `checkRole` | boolean | `true` | Detect role-based addresses (admin@, info@, etc.) |
| `checkFreeProvider` | boolean | `true` | Detect free email providers (Gmail, Yahoo, etc.) |
| `checkCatchAll` | boolean | `false` | Detect catch-all domains (slower, extra SMTP connection) |
| `smtpTimeout` | integer | `10000` | SMTP connection timeout in milliseconds (3,000-30,000) |
| `maxConcurrency` | integer | `5` | Maximum emails to validate simultaneously (1-20) |

## Output Format

Each validated email produces one result:

```
{
  "email": "john@company.com",
  "isValid": true,
  "formatValid": true,
  "mxValid": true,
  "smtpValid": true,
  "isDisposable": false,
  "isRole": false,
  "isFreeProvider": false,
  "isCatchAll": null,
  "mxRecords": [
    { "exchange": "mx1.company.com", "priority": 10 },
    { "exchange": "mx2.company.com", "priority": 20 }
  ],
  "suggestion": null,
  "score": 100,
  "reason": "Valid email address",
  "scrapedAt": "2026-03-11T12:00:00.000Z"
}
```

Example of an invalid email:

```
{
  "email": "user@temp-mail.io",
  "isValid": true,
  "formatValid": true,
  "mxValid": true,
  "smtpValid": null,
  "isDisposable": true,
  "isRole": false,
  "isFreeProvider": false,
  "isCatchAll": null,
  "mxRecords": [{ "exchange": "mx.temp-mail.io", "priority": 10 }],
  "suggestion": null,
  "score": 55,
  "reason": "Valid email with warnings: disposable email domain",
  "scrapedAt": "2026-03-11T12:00:00.000Z"
}
```

## Usage Examples / Use Cases

- **Email list cleaning** — Validate your mailing list before campaigns to remove invalid, disposable, and risky addresses and improve deliverability
- **Sign-up form validation** — Integrate via API to verify email addresses in real time during user registration
- **Lead qualification** — Score incoming leads by email quality, filtering out disposable addresses and prioritizing business domains over free providers
- **CRM data hygiene** — Periodically validate contact emails in your CRM to flag outdated or invalid addresses
- **Fraud prevention** — Block disposable and temporary email addresses from account creation to reduce fake sign-ups
- **Email deliverability auditing** — Check MX records and SMTP responses for your own domain to diagnose sending issues

## Pricing

This actor uses Pay-Per-Event (PPE) pricing: **$1.90 per 1,000 emails validated** ($0.0019 per event).

Pricing includes all platform compute costs — no hidden fees.

## FAQ

### Why does SMTP verification sometimes return null?

SMTP verification connects to port 25 on the target mail server. Some hosting environments (including Apify's infrastructure) may restrict outbound port 25 connections. When this happens, SMTP verification returns `null` (inconclusive) rather than `false`. The email still receives partial credit in the quality score. MX record verification and all other checks still work normally.

### What is the quality score based on?

The score (0-100) is calculated from six factors: valid format (30 points), MX records present (30 points), SMTP verification passed (20 points), not a disposable domain (10 points), not role-based (5 points), and business domain vs free provider (5 points). An email with valid format, working MX, confirmed SMTP, from a business domain scores 100.

### How accurate is disposable email detection?

The actor checks against a database of 30,000+ known disposable and temporary email domains. The list covers major providers like Guerrilla Mail, Temp Mail, Mailinator, and thousands of lesser-known services. New disposable providers appear constantly, so some very new services may not be in the list.

### Can I validate thousands of emails in one run?

Yes. The actor deduplicates your list automatically and processes emails concurrently based on `maxConcurrency`. For large lists (10,000+), increase the run timeout and consider disabling `checkSmtp` and `checkCatchAll` for faster processing since those require network connections per email.

### What does the suggestion field do?

If the actor detects a likely typo in the domain part of the email (e.g., `user@gmial.com` instead of `user@gmail.com`), it returns a corrected suggestion in the `suggestion` field. This helps you fix common data entry errors rather than simply marking the email as invalid.

## Related Actors

- [Disposable Email Checker](https://apify.com/junipr/disposable-email-checker) — Focused disposable domain detection if you only need that check
- [Contact Info Scraper](https://apify.com/junipr/contact-info-scraper) — Extract emails from websites, then validate them with this actor
- [Domain WHOIS Lookup](https://apify.com/junipr/domain-whois-lookup) — Get WHOIS registration and DNS data for email domains
- [Yellow Pages Scraper](https://apify.com/junipr/yellow-pages-scraper) — Scrape business listings with contact emails to validate
- [RAG Web Extractor](https://apify.com/junipr/rag-web-extractor) — Extract structured content from websites for AI and data pipelines