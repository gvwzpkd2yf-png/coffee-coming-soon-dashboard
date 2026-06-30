# Coming Soon Coffee — Charlotte / Metrolina

A dashboard tracking coffee shops about to open (or that just opened) across Charlotte and the surrounding Metrolina, NC area — built for [Actually Fun Coffee](https://actuallyfunemail.com) to spot new shops early and be among the first to review them.

- `index.html` — the dashboard. Reads data from `data/coming-soon-leads.json` at runtime via `fetch()`, so it must be served over http(s) (GitHub Pages, any static host, or a local dev server) — opening it directly as a `file://` path won't load the data.
- `data/coming-soon-leads.json` — the current list of tracked shops (name, city, status, confidence, sources, notes). Refreshed on a recurring schedule.
- `research-playbook.md` — the repeatable research SOP used to refresh the data: which sources to check, search query templates, dedup rules, status-upgrade rules, and confidence scoring.

## Updating data

The data file is meant to be refreshed regularly (e.g. weekly) by re-running the playbook in `research-playbook.md` and committing the updated `data/coming-soon-leads.json`.
