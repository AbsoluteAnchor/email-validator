[Email Validator](https://apify.com/maged120/email-validator?fpr=data)

# Email Validator Actor

This Apify actor validates a list of email addresses leveraging Apify's residential proxy for each request. For each email, it returns information about format, domain, disposability, DNS, whitelist status, and retry attempts.

## Input

The input must be a JSON object with the following fields:

- `emails` (array, required): List of email addresses to validate (max 10,000).
- `batch_size` (integer, optional): Number of emails to process concurrently in each batch (default: 10, min: 1, max: 50).
- `delay_between_requests` (integer, optional): Delay in seconds between processing batches to avoid rate limiting (default: 0, min: 0, max: 60).
- `max_retries` (integer, optional): Maximum number of retry attempts for failed requests (default: 3, min: 1, max: 10).

Example input:

```
{
  "emails": [
    "ewweewwew@gmail.com",
    "test@example.com"
  ],
  "batch_size": 10,
  "delay_between_requests": 0,
  "max_retries": 3
}
```

## Output

The output is a dataset where each item corresponds to an email and contains the following fields:

```
{
  "email": "ewweewwew@gmail.com",
  "status": "success",
  "format": true,
  "domain": "gmail.com",
  "disposable": false,
  "dns": true,
  "whitelist": true,
  "attempts": 1,
  "error": null
}
```

If an error occurs for an email, the `status` and `error` fields will describe the issue, and the other fields may be null. Example error output:

```
{
  "email": "bad@email",
  "status": "invalid_format",
  "error": "Invalid email format",
  "format": false,
  "domain": "",
  "disposable": null,
  "dns": null,
  "whitelist": null,
  "attempts": null
}
```

## How it works

- Reads a list of emails from input.
- Validates email format locally before making API request.
- Processes emails in batches (concurrent requests per batch controlled by `batch_size`).
- Waits for `delay_between_requests` seconds between batches to avoid rate limiting.
- For each email, calls the api
- Retries failed requests up to `max_retries` times, with exponential backoff for certain errors.
- Stores the results in the dataset, including the number of attempts and any error messages.

## Proxy Usage

The actor uses Apify's residential proxy group (`RESIDENTIAL`, country code `US`) for all requests. Each request uses a new proxy URL to maximize success and avoid rate limiting or blocking.