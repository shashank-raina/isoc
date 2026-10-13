# ISOC calculator

A single-page calculator for Microsoft's Integrated Security Operations Center: what it costs, what the licence includes, and whether moving off Microsoft Sentinel pays.

No build step, no dependencies, no analytics, no cookies. One HTML file with the CSS and JavaScript inline. Google Fonts is the only external request. Nothing the visitor types leaves their browser.

## Why it exists

Every other ISOC page explains what was announced. None of them let you put your own ingestion profile in and see a number. The model also makes the open question explicit rather than hiding it: Microsoft names some Microsoft data as included and leaves several of the largest tables in a typical tenant unnamed, so the page returns a range and prices the gap.

## Two modes

The Sentinel checkbox forks the page.

**With Sentinel today.** Compares staying against moving. Your Defender raw logs and the unnamed Microsoft tables are paid today, offset by the E5 and E7 benefit allowance of 5 MB per user per day. Under ISOC the Defender data is never ingested, the allowance is gone, and non-Microsoft data is billed at the ISOC rate.

**Without Sentinel.** There is no current bill to compare against and no allowance to give up, so the page prices ISOC on its own and the stay column, the seat count and the benefit field are hidden.

## The model

```
allowance   = seats × benefitMB ÷ 1024          GB/day
msBillable  = max(0, defenderGB + unnamedGB − allowance)

stay        = (msBillable + nonMicrosoftGB) × sentinelRate
isocBest    =  nonMicrosoftGB × isocRate
isocWorst   = (nonMicrosoftGB + unnamedGB) × isocRate
```

Months are 30.44 days. Figures are ingestion only. Retention beyond the included window, the data lake tier, Fabric capacity and anything priced per user are all out of scope, and so is the engineering cost of moving detection content, which in year one is usually larger than the ingestion difference.

## Rates

Every rate is an input on the page, so a visitor can correct it without touching the code. The defaults are US list prices in USD as at October 2026:

| Input | Default | Source |
|---|---|---|
| Sentinel ingestion | $4.30 / GB | Pay-as-you-go list, analytics tier |
| ISOC non-Microsoft ingestion | $2.40 / GB | Announced ISOC rate |
| Benefit allowance | 5 MB / user / day | E5 and E7 Sentinel benefit |

To change a default, edit the `value` attribute on the matching input in `index.html`. The preset buttons live in the `presets` object in the script at the bottom of the same file.

## Deployment

Cloudflare Pages, connected to this repo.

- Framework preset: **None**
- Build command: **leave empty**
- Build output directory: **`/`**

`_headers` is read by Pages at build time and sets the security headers and a content security policy scoped to an inline-everything page. `robots.txt` and `sitemap.xml` are there so the page indexes cleanly.

## Before first publish

Three placeholders need the real hostname:

- `index.html`: `CANONICAL_HOST` appears twice, in the canonical link and in `og:url`. `SITE_NAME` appears once in `og:site_name`
- `robots.txt`: `CANONICAL_HOST` in the sitemap line
- `sitemap.xml`: `CANONICAL_HOST` in `loc`

There is no `og:image` yet, so a LinkedIn or Slack share renders as a plain link card. Add a 1200×630 image and an `og:image` tag if this gets pushed hard.

## Accuracy

Preview scope and pricing move quickly. The page carries a footer saying so. When Microsoft publishes the table list, the honest fix is to replace the range with a single figure and delete the two-scenario machinery rather than leave a resolved question looking open.
