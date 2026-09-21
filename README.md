# GPX Viewer

A small Flask web app that scans a folder of `.gpx` files (e.g. exported from
a fitness tracker or GPS watch), extracts all the location points, and plots
them as paths on an interactive Folium/Leaflet map in your browser.

## How it works

1. Upload `.gpx` files via the web UI (or drop them into `data/GPX/`).
2. Click **Scan** — this parses any new GPX files, appends their points to
   `output/locations.csv`, and splits them into separate "paths" whenever
   there's a gap of more than 30 minutes between points.
3. The app renders those paths as blue lines on a map (`templates/map.html`),
   which you can view embedded on the home page or open in a new tab.
4. **Regenerate** clears the cached points/map and re-processes everything
   from scratch.

Already-processed files are tracked in `output/completed.txt` so re-scanning
only picks up new files.

## Requirements

- Python 3.11+
- Packages in `requirements.txt`: `gpxpy`, `folium`, `flask`, `gunicorn`

## Running locally

```bash
pip install -r requirements.txt
flask run
```

Then open `http://127.0.0.1:5000` in your browser.

- `/upload` — upload GPX files (saved to `data/GPX/`)
- `/` — home page with map + Scan/Regenerate controls
- `/map` — the raw generated map

## Running with Docker

```bash
docker build -t gpx-viewer .
docker run -p 5000:5000 gpx-viewer
```

## Project structure

| File/folder       | Purpose                                      |
|--------------------|-----------------------------------------------|
| `app.py`           | Flask routes (`/`, `/map`, `/scan`, `/regen`, `/upload`) |
| `main.py`          | Orchestrates the scan/regenerate pipeline    |
| `gpx_manager.py`   | Parses GPX files into `output/locations.csv` |
| `map_manager.py`   | Groups points into paths and builds the Folium map |
| `templates/`       | HTML pages (Jinja2)                          |
| `static/`          | CSS and icon assets                          |
| `data/GPX/`        | Where uploaded/raw GPX files live (gitignored) |
| `output/`          | Generated `locations.csv` and `completed.txt` (gitignored) |

## Notes

- `data/`, `output/`, and `templates/map.html` are gitignored — they're
  generated/user data, not part of the source.
- The map defaults to a center point over the UK (`52.076024, -2.401188`)
  when no data has been loaded yet.
