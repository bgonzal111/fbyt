# fbyt

Read-only tooling for a single private Yahoo fantasy football league, built for
personal use by that league's commissioner. Not distributed, no user accounts,
no hosted service.

## What it does

Pulls league data from the Yahoo Fantasy Sports API, caches it locally as
weekly snapshot files, and generates written summaries from those snapshots.

**Data pulls**
- League settings and metadata
- Team rosters
- Weekly matchups and scoring
- Transactions (adds, drops, trades, waiver claims)
- Standings

**Generated output**
- Weekly recaps
- Power rankings
- Trade and waiver summaries
- Playoff scenario breakdowns

All output is plain text meant to be pasted into a group chat. Nothing is
published, hosted, or served to anyone else.

## How it works

1. OAuth 2.0 authorization code flow against Yahoo, `redirect_uri=oob`.
   Authorize once in a browser, paste the code back, exchange for access and
   refresh tokens.
2. Scheduled local script pulls the current week's league state and writes a
   timestamped JSON snapshot to `snapshots/`.
3. Report generators read from snapshots, never from live API calls. If a
   snapshot for a given week is missing, the generator reports the gap rather
   than falling back to older data.

Yahoo's JSON is deeply nested and inconsistently shaped — the same key can
return a list or a dict depending on context — so the parsing layer asserts on
shape before indexing.

## Scope

- Single league, single Yahoo account.
- Read-only. No roster moves, settings changes, or posting.
- Rate limiting respected; pulls run once weekly plus occasional ad-hoc
  refreshes, well under any per-hour ceiling.

## Setup

Requires a Yahoo Developer application with Fantasy Sports read access.

```
cp .env.example .env
# fill in YAHOO_CLIENT_ID and YAHOO_CLIENT_SECRET
python auth.py       # one-time browser authorization, writes tokens to .env
python pull.py --week N
```

Credentials live in `.env` and are never committed. See `.gitignore`.

## Status

Early. Auth and league/roster pulls first, report generation after.
