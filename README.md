# 🎬 Stremio Arabic Movies & Series Addon

A Stremio community addon that aggregates Arabic movies and TV series from multiple Arabic streaming websites into a single unified catalog.

**مشاهدة الأفلام والمسلسلات العربية من مصادر متعددة في تطبيق Stremio**

## 🔗 Supported Sources

| Source | Description | Category |
|--------|-------------|----------|
| **Akwam** | Largest Arabic content library | Movies, Series, Anime |
| **Egybest** | Arabic-subtitled & dubbed content | Movies, Series |
| **ArabCity** | Arabic movies and series | Movies, Series |
| **WatanFlix** | Arabic TV series and movies | Movies, Series |
| **Cima4u / CimaClub** | Popular Arabic streaming | Movies, Series |
| **MyCima / CimaNow** | Additional Arabic libraries | Movies, Series |
| **FaselHD** | HD Arabic content (like CloudStream) | Movies, Series, Anime, Turkish, Ramadan |
| **EgyDead** | Multi-category Arabic library (like CloudStream) | Movies, Series, Anime, Cartoon, Asian |

## 📦 Categories

- 🎥 **Arabic Movies** (أفلام عربية)
- 📺 **Arabic Series** (مسلسلات عربية)
- 🌙 **Ramadan Series** (مسلسلات رمضان)
- 🇹🇷 **Turkish Dubbed** (مسلسلات تركية مدبلجة)
- 🎌 **Anime** (أنمي)
- 🌏 **Asian Series** (مسلسلات آسيوية)
- 🌍 **Foreign Movies** (أفلام أجنبية مترجمة)
- 📚 **All** (الكل)

## 🚀 Quick Start

### Prerequisites

- Node.js >= 18
- npm or yarn

### Installation

```bash
# Clone the repository
git clone <your-repo-url>
cd stremio-arabic-addon

# Install dependencies
npm install

# Copy environment file
cp .env.example .env

# Edit .env with your settings (optional)
# Add TMDB_API_KEY for better metadata matching

# Build the project
npm run build

# Start the addon
npm start

# Or run in development mode with hot reload
npm run dev
```

The addon will start on port 7000 by default (configurable via PORT env variable).

## 📥 Install in Stremio

Once deployed, install the addon in Stremio:

1. Open Stremio
2. Go to Addons → Community Addons
3. Paste your addon URL: `https://your-domain.com/manifest.json`
4. Click Install

Or use the one-click install link:
```
stremio://your-domain.com/manifest.json
```

## ⚙️ Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | `7000` | Server port |
| `NODE_ENV` | `production` | Environment mode |
| `PUBLIC_URL` | `http://localhost:7000` | Public URL for manifest |
| `CACHE_TTL` | `3600` | Cache TTL in seconds (1 hour) |
| `TMDB_API_KEY` | (optional) | TMDB API key for metadata |
| `OMDB_API_KEY` | (optional) | OMDB API key for metadata |
| `LOG_LEVEL` | `info` | Logging level (debug/info/warn/error) |
| `RATE_LIMIT_PER_MINUTE` | `10` | Max requests per site per minute |
| `MAX_RETRIES` | `3` | Max retries for failed scrapes |

### Site URL Configuration

All site URLs can be updated without code changes. When a site changes its mirror/domain, just update the env variable:

```bash
AKWAM_URL=https://akwam.bond
EGYBEST_URL=https://egybest.show
ARABCITY_URL=https://arabcity.site
WATANFLIX_URL=https://watanflix.com
CIMA4U_URL=https://cima4u.io
MYCIMA_URL=https://mycima.show
FASELHD_URL=https://faselhd.center
EGYDEAD_URL=https://egydead.live
```

## 🏗️ Project Structure

```
src/
├── index.ts              # Main entry point, HTTP server
├── addon.ts              # Addon interface (catalog, meta, stream handlers)
├── config.ts             # Configuration from environment variables
├── manifest.ts           # Stremio manifest.json generation
├── types/
│   └── index.ts          # TypeScript type definitions
├── handlers/
│   ├── catalog.ts        # Catalog handler (list + search)
│   └── stream.ts         # Stream handler (video URL resolution)
├── scrapers/
│   ├── index.ts          # Scraper registry
│   ├── base.ts           # Abstract base scraper class
│   ├── akwam.ts          # Akwam scraper
│   ├── egybest.ts        # Egybest scraper
│   ├── arabcity.ts       # ArabCity scraper
│   ├── watanflix.ts      # WatanFlix scraper
│   ├── cima4u.ts         # Cima4u scraper
│   ├── mycima.ts         # MyCima scraper
│   ├── faselhd.ts        # FaselHD scraper
│   └── egydead.ts        # EgyDead scraper
└── utils/
    ├── cache.ts           # In-memory LRU cache with TTL
    ├── dedup.ts           # Deduplication & filtering
    ├── http.ts            # HTTP client with rate limiting & retries
    ├── logger.ts          # Structured logging (pino)
    └── matcher.ts         # TMDB/OMDB metadata matching & helpers
```

## 🔧 Adding a New Source Site

1. Create a new scraper file in `src/scrapers/` extending `BaseScraper`:

```typescript
import { BaseScraper } from "./base";
import type { ScrapedItem, StreamSource, ContentType } from "../types";
import { config } from "../config";

export class NewSiteScraper extends BaseScraper {
  siteId = "newsite" as const;
  siteName = "New Site";
  baseUrl = config.sites.newsite; // add to config

  async fetchCatalog(type?: ContentType, page?: number): Promise<ScrapedItem[]> {
    // Implement catalog scraping
    return [];
  }

  async search(query: string, type?: ContentType): Promise<ScrapedItem[]> {
    // Implement search
    return [];
  }

  async getStreams(sourceId: string, season?: number, episode?: number): Promise<StreamSource[]> {
    // Implement stream URL extraction
    return [];
  }

  async getDetails(sourceId: string): Promise<ScrapedItem | null> {
    // Implement detail fetching
    return null;
  }
}
```

2. Register in `src/scrapers/index.ts`:

```typescript
import { NewSiteScraper } from "./newsite";

export const scrapers: ScraperModule[] = [
  // ...existing scrapers
  new NewSiteScraper(),
];
```

3. Add the site URL to `.env` and `src/config.ts`.

4. Add to `src/types/index.ts` SourceSite union type.

## 🚢 Deployment

### BeamUp (Stremio Official)

```bash
npm install -g beamup-cli
beamup deploy
```

### Railway

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app)

1. Connect your GitHub repo
2. Set environment variables
3. Deploy automatically

### Render

1. Create a new Web Service
2. Connect your GitHub repo
3. Build command: `npm install && npm run build`
4. Start command: `npm start`
5. Set environment variables

### Vercel

```bash
vercel --prod
```

Note: Vercel's serverless functions have a 10s timeout. For better performance, use Railway or Render.

## 🛡️ Robustness Features

- **Graceful Fallback**: If one source site is down, results from remaining sites are still served
- **Rate Limiting**: Per-site rate limiting prevents being blocked (10 req/min per site)
- **Rotating User-Agents**: Pool of User-Agent strings rotated per request
- **Exponential Backoff**: Failed scrape requests are retried with increasing delays
- **LRU Caching**: Catalog data cached for 1 hour (configurable), streams for 15 minutes
- **Deduplication**: Cross-source deduplication using title similarity (85% threshold) + year matching
- **Mirror Management**: All site URLs configurable via environment variables

## 📊 API Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /manifest.json` | Stremio addon manifest |
| `GET /catalog/:type/:id.json` | Catalog listing |
| `GET /meta/:type/:id.json` | Item metadata |
| `GET /stream/:type/:id.json` | Stream URLs |
| `GET /health` | Health check & stats |
| `GET /cache/stats` | Cache statistics |
| `POST /cache/clear` | Clear all cache |

## 📝 Notes

- All content is indexed from publicly accessible web pages — no logins or paywall bypassing
- The addon only provides HTTP streams already publicly available on those sites
- Site scraping relies on CSS selectors that may need updates if sites change their HTML structure
- For best metadata results, configure TMDB_API_KEY in your environment
- Stream availability depends on the source sites being online

## 📄 License

MIT
