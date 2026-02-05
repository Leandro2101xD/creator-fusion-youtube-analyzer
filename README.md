# YouTube Creator Analyzer for Brand Partnerships

Analyze YouTube creators for brand deals with metrics no other tool provides: **Creator Score**, **sponsorship history detection**, **engagement authenticity signals**, and **rate card generation** — all from a single run.

## What makes this different?

The Apify Store has dozens of YouTube scrapers that pull subscriber counts and view numbers. This actor goes further — it answers the actual question brands pay to answer: **"Should I give this creator money, and how much?"**

| Feature | Other YouTube Scrapers | This Actor |
|---|---|---|
| Channel stats (subs, views) | ✅ | ✅ |
| Video-level engagement metrics | Some | ✅ Detailed |
| **Composite Creator Score (0–100)** | ❌ | ✅ Tier-adjusted |
| **Sponsorship history detection** | ❌ | ✅ Brands, codes, affiliates |
| **Engagement authenticity signals** | ❌ | ✅ Statistical anomaly detection |
| **Sponsorship rate card** | ❌ | ✅ By deal type + niche |
| Proxy cost | $5–15/run | **$0** |
| Browser required | Usually | **No** |

## How it works

1. **You provide** a list of YouTube channels (URLs, @handles, or channel IDs) and your free YouTube Data API key.
2. **The actor fetches** channel details and recent videos using the official YouTube API (zero proxy cost, zero browser cost).
3. **The analytics engine** computes engagement metrics, posting consistency, audience quality, and a weighted Creator Score.
4. **Sponsorship detection** scans video descriptions for #ad disclosures, affiliate links, promo codes, and brand mentions.
5. **Authenticity analysis** runs statistical checks on engagement patterns to flag potentially bought followers or fake likes.
6. **Rate card generation** produces estimated sponsorship pricing by deal type (integration, dedicated, Shorts, usage rights).

## Output per channel

```json
{
    "channelName": "MKBHD",
    "subscriberCount": 19400000,
    "creatorScore": {
        "score": 87,
        "grade": "A",
        "tier": "Mega",
        "breakdown": { "..." }
    },
    "analytics": {
        "engagementRate": 3.42,
        "avgViews": 4200000,
        "postsPerWeek": 2.1,
        "postingConsistency": 82,
        "viewToSubRatio": 21.6
    },
    "sponsorship": {
        "totalDetected": 8,
        "sponsorshipRate": 27,
        "detectedBrands": [
            { "brand": "Dbrand", "mentionCount": 4 },
            { "brand": "NordVPN", "mentionCount": 2 }
        ],
        "disclosureRate": 100,
        "promoCodes": ["MKBHD"]
    },
    "authenticity": {
        "score": 95,
        "label": "High authenticity — no significant red flags",
        "flags": []
    },
    "rateCard": {
        "estimatedIntegrationRate": { "low": 85000, "mid": 120000, "high": 180000 },
        "estimatedDedicatedRate": { "low": 170000, "mid": 240000, "high": 360000 },
        "estimatedShortsRate": { "low": 15000, "mid": 22000, "high": 35000 },
        "adjustments": { "niche": "Technology", "multiplier": 1.3 }
    }
}
```

## Creator Score methodology

The Creator Score (0–100) is a weighted composite of five signals, adjusted for audience tier:

| Signal | Weight | What it measures |
|---|---|---|
| Engagement rate | 35% | Likes + comments relative to views, benchmarked against tier averages |
| Posting consistency | 20% | Standard deviation of upload gaps (regular = higher score) |
| Posting frequency | 15% | Videos per week — sweet spot is 2–5/week |
| View-to-subscriber ratio | 15% | What % of subscribers actually watch each video |
| Audience reach | 15% | Raw subscriber count, tiered |

**Grades:** A+ (90+), A (80+), B+ (70+), B (60+), C+ (50+), C (40+), D (30+), F (<30)

## Sponsorship detection

Scans every video description for:
- **FTC disclosure hashtags**: #ad, #sponsored, #partner, #paidpartnership
- **Sponsorship phrases**: "sponsored by", "brought to you by", "use my code"
- **30+ affiliate networks**: Amazon Associates, Impact, CJ, Awin, ShareASale, and more
- **25+ known sponsor brands**: NordVPN, Squarespace, Audible, HelloFresh, Ridge, AG1, etc.
- **Promo codes**: Extracts actual codes (e.g., "use code MKBHD")

Outputs sponsorship rate (% of videos with deals), disclosure compliance rate, and a list of detected brand partners.

## Authenticity signals

Statistical analysis flags potentially fake engagement:
- **Like consistency check**: Bots apply likes at fixed rates → unnaturally low variance across videos
- **Comment-to-like ratio**: Like bots rarely buy comment bots → abnormally low ratio
- **Subscriber-to-view ratio**: Bought subscribers don't watch → very low lifetime views per sub
- **Zero-comment anomaly**: Videos with many likes but zero comments
- **View distribution**: Bought views create unnaturally flat view counts

Score: 0–100 (higher = more authentic). Channels scoring below 40 have strong indicators of inauthentic engagement.

## Cost

**Near zero.** This actor uses the YouTube Data API (HTTP requests only) — no browser instances, no residential proxies.

- **API quota**: ~4 units per channel. Free tier provides 10,000 units/day = ~2,500 channels/day.
- **Compute**: ~128MB RAM. A typical run of 100 channels takes under 2 minutes.
- **Your cost**: Effectively the Apify platform fee only.

## Getting your API key

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a project (or select existing)
3. Enable "YouTube Data API v3" from the API Library
4. Create an API key under Credentials
5. Paste it into the actor's input

The free tier gives you 10,000 quota units per day — enough for ~2,500 channels.

## Use cases

- **Brand/agency**: Evaluate creator rosters for campaigns with composite scores and rate cards
- **Influencer marketing platform**: Bulk-analyze creators for marketplace listings
- **Creator themselves**: Understand your own metrics and benchmark against tier averages
- **Competitive research**: Analyze competitor creator partnerships via sponsorship detection

## Input

| Field | Type | Default | Description |
|---|---|---|---|
| `apiKey` | string | (required) | YouTube Data API v3 key |
| `channels` | string[] | (required) | Channel URLs, @handles, or IDs |
| `videosPerChannel` | integer | 30 | Recent videos to analyze (5–200) |
| `enableSponsorshipDetection` | boolean | true | Scan for sponsorship indicators |
| `enableAuthenticityCheck` | boolean | true | Run engagement authenticity analysis |
| `enableRateCard` | boolean | true | Generate sponsorship rate cards |
| `minSubscribers` | integer | 0 | Skip channels below threshold |
| `minEngagementRate` | number | 0 | Skip channels below engagement % |

## Built by Creator Fusion LLC

This actor is part of the [Creator Fusion](https://creatorfusion.com) platform — business management tools for content creators.
