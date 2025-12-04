# Fresh Tomatoes Script Playbook

Practical commands to seed publications/critics, discover listing URLs, generate recipes, and scrape reviews.

## Seeds (RT publications & critics)
- `python manage.py seed_publications` — scrape RT-approved publications into the DB.
- `python manage.py seed_critics` — scrape RT critics/authors and link to pubs when possible.

## Listing URL discovery
- `python manage.py discover_indices` — run heuristics (URL patterns, homepage nav, RSS) for all pubs.
  - Single pub: `python manage.py discover_indices --pub-id <id>`
  - Limit batch: `python manage.py discover_indices --limit 20`
- Placeholder base URLs enrichment:
  - `python manage.py fetch_search_results [--limit N]` — cache Serper results for pubs needing URLs.
  - `python manage.py extract_review_urls [--limit N]` — LLM parses cached results to propose listing URLs.
  - `python manage.py enrich_urls [--limit N]` — Serper+LLM enriches base URLs directly.

## Recipe generation & validation
- `python manage.py generate_recipe --pub-id <id> --sample-url <article_url>` — fetch sample page and build an extraction recipe via LLM.
- `python manage.py test_extraction --pub-id <id> --url <article_url>` — validate the active recipe on a URL.

## Extraction / monitoring
- Runtime extractor uses stored recipes; for inspection use the dashboards:
  - `streamlit run dashboard.py` — extraction pipeline stats (expects `data/extraction_only_*`, `date_analysis_results.json`).
  - `streamlit run dashboard/review_listings_dashboard.py` — listing URL coverage (expects `data/publications_consolidated.json`, schemas/logs under `logs/recipe_generation/...`).
  - `streamlit run dashboard/monitoring_dashboard.py` — monitoring snapshots (`logs/monitoring/snapshots/*.json`, `monitoring/monitoring.db`).

## Rotten Tomatoes review scraper (RT site)
- `python scrapers/rotten_tomatoes_scraper.py` — async Playwright scraper for RT movie review pages; paginates “Load More” and extracts rows. Set the target URL inside the script or extend it to accept a CLI argument.

## Regenerating archived artifacts
- Coverage/RT: `scripts/compare_rt_coverage.py`, `scripts/analyze_rt_coverage_gaps.py`, `scripts/analyze_wicked_coverage.py`
- Stale/diagnostics: `scripts/analyze_extraction_dates.py`, `scripts/generate_stale_fix_plan.py`, `scripts/diagnose_stale_recipes.py`, `scripts/research_stale_urls.py`, `scripts/identify_inactive_pubs.py`
- YouTube: `scripts/fix_youtube_channels.py`, `scripts/run_youtube_pipeline.py`
- RT review snapshots: rerun the RT scraper for fresh-dated files
- Store regenerated outputs in `artifacts/<date>/` to keep the root clean.
