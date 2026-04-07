# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file static web app (`index.html`) that visualizes daily download statistics for CRAN (R) and PyPI (Python) packages side by side using Chart.js. No build step, no dependencies to install — just open `index.html` in a browser.

## Development

Open `index.html` directly in a browser or serve it locally:

```sh
python -m http.server 8000
# then visit http://localhost:8000
```

There is no build system, linter, test suite, or package manager. All code (HTML, CSS, JS) lives in `index.html`.

## Architecture

The app is a single IIFE in a `<script>` tag at the bottom of `index.html`. Key components:

- **API layer**: Fetches from cranlogs (`cranlogs.r-pkg.org`) and pypistats (`pypistats.org`) through a CORS proxy (`corsproxy.io`). `fetchWithRetry()` handles retries, rate limiting, and HTML error responses.
- **State**: `packages` array holds `{ name, registry, color, rawData }` objects. State is persisted to URL hash (`#packages=cran:ggplot2,pypi:numpy`) and localStorage.
- **Chart**: Chart.js time-series line chart with optional 7-day rolling average smoothing.
- **UI**: Registry toggle (CRAN/PyPI), package input with colored chips, time range selector (1W/1M/3M/6M).

## Key Behaviors

- CRAN package names are **case-sensitive** (the cranlogs API requires exact casing). PyPI names are lowercased.
- Packages are fetched **sequentially** during refresh to avoid rate limits from the APIs.
- CDN dependencies: Chart.js 4.x and chartjs-adapter-date-fns 3.x loaded from jsdelivr.
