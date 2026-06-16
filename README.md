[Email Validator](https://apify.com/snipercoder/email-validator?fpr=data)

# Email Finder Scraper

Finds professional email addresses for individuals at companies.

## Features

- **High Speed Batch Processing**: Processes 50 searches concurrently for maximum throughput.
- **Automated Verification**: Uses advanced techniques to verify email deliverability.
- **Browser-Like Behavior**: Mimics real user interaction to ensure reliable results.
- **CSV & Bulk Support**: Upload a CSV file or use single entry mode.
- **Proxy Support**: Optimized for Residential Proxies (Automatically handled).

## Input

- **Name**: Full name of the prospect (Single Search).
- **Domain**: Company domain (Single Search).
- **CSV File**: Upload a CSV with `name` and `domain` columns for bulk processing.

## Output

The actor pushes search results to the default dataset for each individual query:

- `Name`: Full name of the prospect.
- `Domain`: Company domain.
- `Email`: Found professional email address (if successful).
- `Validation`: Verification status (e.g., 'valid', 'catch-all').
- `Found`: Boolean indicating success.
- `Status Code`: The HTTP response code from the search attempt.

## Usage

Simply input the list of names and domains you want to find emails for, or upload a CSV. The actor will handle the rest, solving captchas in parallel and retrieving results efficiently.