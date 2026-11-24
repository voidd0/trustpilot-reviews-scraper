# Trustpilot Reviews Scraper: export to CSV or JSON with Python, Node.js or curl

Scrape Trustpilot reviews by business slug: stars, title, text, language, dates and owner reply, plus sentiment tags for praise and complaint themes. Star-filter pagination reaches more reviews than a single feed, and delta mode returns only reviews that are new since your last run.

This repository has ready scripts for the [Trustpilot Reviews Scraper: New Reviews, Star Filter](https://apify.com/v0iddo/trustpilot-reviews-deep) Actor on Apify. The Actor does the
fetching, paging and cleaning; the scripts here run it with your input and save the results as a CSV or JSON file.

## Quick start

1. Create an Apify account and copy your API token from
   [Console > Settings > Integrations](https://console.apify.com/settings/integrations).
2. Install the client and run the script with the example input:

```bash
pip install "apify-client>=2"
export APIFY_TOKEN=your_token
python run.py input.json -o results.csv
```

Edit `input.json` for your own run. You can also run the Actor in the browser on its
[Apify page](https://apify.com/v0iddo/trustpilot-reviews-deep), schedule it there, and connect its output to Google Sheets, Slack, Zapier or Make.

## Input

The example input (`input.json`):

```json
{
  "businesses": [
    "apify.com"
  ],
  "maxReviewsPerBusiness": 20
}
```

| Field | Type | What it does |
| --- | --- | --- |
| `businesses` | array | Business slug like "apify.com" or full URL like "https://www.trustpilot.com/review/apify.com". |
| `maxReviewsPerBusiness` | integer | Hard cap per business. Pages through results until cap reached or exhausted. |
| `useStarFilterPagination` | boolean | Loop the same business with stars=1..5 filter to multiply effective depth. |
| `language` | string | Two-letter language filter, e.g. |
| `deltaMode` | boolean | Stores per-business seen reviewIds in this actor's KV store. |

## Output

One row per result. A real row from a run of the example input (more in
[`examples/sample-output.json`](examples/sample-output.json)):

```json
{
  "businessSlug": "apify.com",
  "reviewId": "6b0463d9605f40a9268a04b9",
  "stars": 5,
  "title": "Apify has saved me so many hours of…",
  "text": "Apify has saved me so many hours of manual work. Having ready-made scrapers in their store means I don't have to build anything from scratch, and connecting them to my automation tools like n8n is super smooth. The platform is reliable, the interface is clean, and getting stru...",
  "language": "en",
  "dateCreated": "2026-11-23T18:03:37.000Z",
  "dateExperience": "2025-11-28T00:00:00.000Z",
  "verified": null,
  "ownerReply": null,
  "url": "https://www.trustpilot.com/reviews/6b0463d9605f40a9268a04b9",
  "sentimentTags": []
}
```

Fields: `businessSlug`, `reviewId`, `stars`, `title`, `text`, `language`, `dateCreated`, `dateExperience`, `verified`, `ownerReply`, `url`, `sentimentTags`.

## Node.js

```javascript
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });
const run = await client.actor('v0iddo/trustpilot-reviews-deep').call({"businesses": ["apify.com"], "maxReviewsPerBusiness": 20});
const { items } = await client.dataset(run.defaultDatasetId).listItems();
```

Full example: [`examples/node/run.mjs`](examples/node/run.mjs).

## Any language: one HTTP request

```bash
curl -X POST "https://api.apify.com/v2/acts/v0iddo~trustpilot-reviews-deep/run-sync-get-dataset-items" \
  -H "Authorization: Bearer $APIFY_TOKEN" -H 'Content-Type: application/json' \
  -d '{"businesses": ["apify.com"], "maxReviewsPerBusiness": 20}'
```

The response is the JSON array of results.

## Cost

The Actor is paid per result: **$0.30 per 1,000 results**, nothing per run. A new Apify account gets $5 of platform credit every month.

## License

MIT for the code in this repository. Made by [vøiddo](https://voiddo.com).
