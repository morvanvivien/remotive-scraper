# How to Scrape Remotive: Remote Job Postings (Category, Eligible Countries, Employment Type)

> **Run it now, no code: [remotive-jobs-scraper on the Apify Store](https://apify.com/bibian/remotive-jobs-scraper)** &nbsp;|&nbsp; $0.60 per 1,000 job postings &nbsp;|&nbsp; no browser, no login

Remotive is one of the best-known remote-only job boards. It has no public export for third parties. This guide shows how to get its postings as clean structured data: title, company, category, employment type and the countries eligible to apply, with a free Apify Actor, from the console or from code.

Searches this page answers: remotive scraper, remote jobs scraper, remotive api, remote job postings json.

## What you get from the Remotive scraper

- Every open remote posting, newest first, with title, hiring company and its website.
- Category (for example Marketing, Software Development) and employment type (full-time, contract...).
- `applicantCountries`: the countries Remotive says are eligible to apply, when the posting restricts them.
- Optional full description as plain text, with any loose contact details removed.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five results, personal data removed, long lists and texts truncated) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | Remotive posting id and public link |
| `title`, `company`, `companyUrl` | Job title, hiring company and its website |
| `category` | Remotive's own job category |
| `employmentType` | `FULL_TIME`, `CONTRACTOR`... |
| `remote` | Always `true` for Remotive's telecommute postings |
| `applicantCountries` | Array of eligible country codes, `null` when the posting is open to anyone |
| `datePosted`, `validThrough` | ISO dates |
| `description` | Plain text, contact details removed |

## Use cases

- **Track remote hiring demand by category.** Count postings per `category` over time to see which skills are in demand remotely.
- **Build a daily remote job alert.** Schedule a run every morning and send only the newest postings to Slack, e-mail or a Google Sheet.
- **Filter by eligibility.** Keep only postings whose `applicantCountries` includes your own country, or that have none (open to all).
- **Feed a niche remote job board.** Use the structured fields and the canonical `url` to power your own aggregator.

## Input

| Field | What it does |
|---|---|
| `keywords` | Keep only postings whose title, company, category or description contain this text, for example `react` or `marketing`. |
| `maxItems` | The run stops once this many postings are saved (default 50). |
| `includeDescriptions` | Adds the full job description as plain text. |

Example input:

```json
{
  "keywords": "react",
  "maxItems": 50,
  "includeDescriptions": true
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/remotive-jobs-scraper) and click **Try for free** (a free Apify account is enough).
2. Fill in the input form (keywords and the maximum number of results) and click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For a daily alert, open **Schedules**, create a daily schedule for the Actor and, optionally, add an integration that sends new results to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/remotive-jobs-scraper').call({
    "keywords": "react",
    "maxItems": 50,
    "includeDescriptions": true
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.company} | ${item.category} | remote: ${item.remote}`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/remotive-jobs-scraper").call(run_input={
    "keywords": "react",
    "maxItems": 50,
    "includeDescriptions": True,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('company')} | {item.get('category')} | remote: {item.get('remote')}")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$0.60 per 1,000 job postings** (that is $0.0006 per job posting), plus a tiny start fee of $0.00005 per run. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/remotive-jobs-scraper) when this guide was written; the Store page is the reference.

## Limits and honest notes

- Remotive's API is intentionally rate-limited by its publisher for third-party access; the Actor reads the open sitemap and each posting's own page instead, so runs stay well inside the site's own terms.
- Only what Remotive publishes is collected. Not every posting restricts `applicantCountries`; most are open to anyone, so the field is often `null`.
- The dataset contains job postings only. Any loose e-mail address or phone number found inside a posting's own text is removed from `description`.
- Postings are the public ones: anything behind a login is out of scope.
- This is an unofficial tool, not affiliated with Remotive. Check the site terms and the data protection rules that apply to your use.

## FAQ

**Does it need a browser or a proxy?**  
No. The Actor reads the site's public sitemap and posting pages directly.

**Does it work without `keywords`?**  
Yes. Leave it empty for a productive default run across the newest postings.

**Can I get the data in Excel or Google Sheets?**  
Yes. Export the dataset as CSV, Excel or JSON from the Apify console, or connect the Apify Google Sheets integration.

## Try it

**[Open remotive-jobs-scraper on the Apify Store](https://apify.com/bibian/remotive-jobs-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
