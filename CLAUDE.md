# Market Event Intelligence

A personal trading-news alert system built on a fork of ActuallyFreeAPI (github.com/mestrovicjozo/ActuallyFreeAPI, MIT license). Goal: collect financial news, tag which stocks each story is about, and send Telegram alerts for tickers I care about. Budget: $0 in paid APIs. Research and paper trading only. No real orders, ever, unless I explicitly say so.

## About me
I'm new to coding and have only used Claude Code a few times. I'm on Windows using PowerShell. Explain what you're doing in plain language, keep changes small, and tell me how to test each change myself before moving on.

## Stack (already in the repo)
- Next.js 16 + TypeScript, Supabase (free Postgres), rss-parser
- News collector: app/api/cron/fetch-rss/route.ts
- Feed list: config/rss-feeds.ts (24 feeds)
- Ticker matching: lib/extractTickers.ts, lib/ticker-mappings.ts
- Database schema: database/schema.sql plus the migration-*.sql files

## Known issues
- README mentions .env.example and DEPLOYMENT.md, but neither exists. Needed env vars: NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY, CRON_SECRET
- News collection runs only once a day (vercel.json: "0 0 * * *").
- First local collector run (stage 1): 20 of 24 feeds worked, 838 articles added. These 4 feeds failed: Yahoo Finance, Yahoo Finance Top Stories, Investopedia News, SEC EDGAR 8-K Filings. Cause not yet investigated (likely blocking or format issues). SEC filings are planned for stage 5 via EdgarTools, so the EDGAR feed can be dropped then.
- Ticker matcher has false positives on common words, e.g. "Target misses on earnings, cuts outlook" -> ["MSFT","ON","TGT"] and "new price target for Microsoft" -> ["A","MSFT","TGT"]. Single-letter and common-word tickers need stricter rules.

## Build order (one stage at a time, get it working, commit)
1. Run locally with my own Supabase project (schema + migrations applied)
2. Run the collector locally on a schedule (every 5-15 min) instead of daily
3. Telegram alerts for a watchlist of tickers
4. Fix ticker false positives, with a small test file of real headlines
5. SEC filings via EdgarTools (Python)
6. Price reaction tracking + backtesting
7. Optional: local AI classification (Laya) only if rules aren't enough

## Rules
- Never commit secrets. Keep keys in .env.local (verify it's gitignored).
- Commit after every working change with a clear message.
- Before adding any new library or repo, check its license and that it actually runs.
