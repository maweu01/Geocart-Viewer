# GeoCart GIS Platform
### Professional KML/KMZ Geospatial Viewer & Analysis System

> A lightweight browser-based GIS workstation for surveyors, drone operators, GIS analysts, and field engineers. Runs entirely on static hosting — no server, no database, no authentication.

---

## Live Deployment

| Method | URL |
|--------|-----|
| GitHub Pages | `https://<username>.github.io/<repo>/` |
| Vercel | `https://<project>.vercel.app/` |
| Netlify | `https://<project>.netlify.app/` |
| Localhost | `file:///path/to/index.html` |

---

## Quick Deploy → GitHub Pages

```bash
# 1. Create a new GitHub repository (public or private)
# 2. Clone it locally
git clone https://github.com/<username>/<repo>.git
cd <repo>

# 3. Copy index.html into the repo root
cp /path/to/geocart-gis/index.html .

# 4. Commit and push
git add index.html
git commit -m "Deploy GeoCart GIS Platform"
git push origin main

# 5. Enable GitHub Pages
#    Repository → Settings → Pages → Source: Deploy from branch → main → /(root) → Save
#    Wait ~60 seconds → live at https://<username>.github.io/<repo>/
```

---

## System Architecture

```
geocart-gis/
├── index.html          ← Complete self-contained application (this file)
│                         All CSS, JS, and HTML in one file.
│                         Zero build tools. Zero npm. Zero backend.
│
└── README.md           ← This file
```

### CDN Dependencies (loaded automatically)

| Library | Version | Purpose |
|---------|---------|---------|
| [Leaflet](https://leafletjs.com) | 1.9.4 | Map rendering engine |
| [JSZip](https://stuk.github.io/jszip/) | 3.10.1 | KMZ (ZIP) extraction |
| [toGeoJSON](https://github.com/tmcw/togeojson) | 5.8.1 | KML → GeoJSON / GPX → GeoJSON |
| [Turf.js](https://turfjs.org) | 6.x | Client-side spatial calculations |

All CDN libraries load from high-availability endpoints (unpkg, cdnjs, jsdelivr).  
An internet connection is required at first load. After that, modern browsers cache the CDN assets.

---

## Supported File Formats

| Format | Extension | Notes |
|--------|-----------|-------|
| KML | `.kml` | Full KML 2.2 support. Styles parsed where available. |
| KMZ | `.kmz` | Auto-extracted. Finds `doc.kml` / `index.kml` inside the archive. |
| GeoJSON | `.geojson` `.json` | RFC 7946 compliant. FeatureCollection, Feature, and bare geometry. |
| GPX | `.gpx` | Waypoints, tracks, and routes. Converted to GeoJSON automatically. |

Multiple files can be loaded simultaneously as separate layers.

---

## Module Reference

### `MapEngine`
Initialises and manages the Leaflet map instance.

| Method | Description |
|--------|-------------|
| `MapEngine.init()` | Initialise map, basemaps, controls, and event listeners |
| `MapEngine.switchBasemap(name)` | Switch active basemap: `'osm'` `'satellite'` `'dark'` `'topo'` |
| `MapEngine.zoomToAll()` | Fit map to all visible layers |

### `FileEngine`
Parses uploaded spatial files to GeoJSON.

| Method | Description |
|--------|-------------|
| `FileEngine.processFile(file)` | Main parser — returns `{name, geojson}` Promise |
| `FileEngine._extractKMZ(file)` | Unzip KMZ, extract primary KML string |
| `FileEngine._parseKML(text)` | DOM parse → toGeoJSON conversion |
| `FileEngine._parseGPX(text)` | DOM parse → toGeoJSON conversion |
| `FileEngine._parseGeoJSON(text)` | JSON parse + structural validation |
| `FileEngine._normalise(data)` | Normalise any GeoJSON type to FeatureCollection |

### `LayerManager`
Manages the layer stack, panel UI, and Leaflet rendering.

| Method | Description |
|--------|-------------|
| `LayerManager.add(name, geojson)` | Add a new layer to the map |
| `LayerManager.remove(id)` | Remove a layer by ID |
| `LayerManager.toggleVisibility(id)` | Show/hide a layer |
| `LayerManager.setOpacity(id, 0–1)` | Adjust layer opacity |
| `LayerManager.zoomTo(id)` | Fit map to layer bounds |
| `LayerManager.rename(id, name)` | Rename a layer |
| `LayerManager.getById(id)` | Retrieve layer descriptor |

### `Inspector`
Feature attribute inspection panel.

| Method | Description |
|--------|-------------|
| `Inspector.inspect(feature, sub, layerObj)` | Open inspector for a clicked feature |
| `Inspector.close()` | Close the inspector panel |
| `Inspector.copyAllAttributes()` | Copy all attributes as JSON to clipboard |
| `Inspector.exportFeature()` | Download the selected feature as GeoJSON |
| `Inspector.zoomToFeature()` | Zoom map to the selected feature |

### `MeasurementTools`
Client-side measurement using Turf.js.

| Method | Description |
|--------|-------------|
| `MeasurementTools.start('distance')` | Activate distance measurement tool |
| `MeasurementTools.start('area')` | Activate area measurement tool |
| `MeasurementTools.start('coord')` | Activate coordinate picker |
| `MeasurementTools.stop()` | Deactivate tool and clear drawings |

### `ExportEngine`
Client-side file export (no server required).

| Method | Description |
|--------|-------------|
| `ExportEngine.downloadAllGeoJSON()` | Export all layers as one GeoJSON |
| `ExportEngine.downloadAllKML()` | Export all layers as one KML |
| `ExportEngine.downloadLayerGeoJSON(id)` | Export a specific layer as GeoJSON |
| `ExportEngine.downloadLayerKML(id)` | Export a specific layer as KML |
| `ExportEngine.showLayerChoice(id)` | Show per-layer format selector |

### `UI`
UI controller — notifications, modals, drag-and-drop, keyboard shortcuts.

| Method | Description |
|--------|-------------|
| `UI.init()` | Wire up all event listeners |
| `UI.notify(msg, type, duration)` | Show a toast notification |
| `UI.toggleLayerPanel()` | Collapse / expand the layer panel |
| `UI.openRenameModal(id, name)` | Open layer rename dialog |
| `UI.showBBox()` | Display bounding extent of all visible layers |
| `UI.copyText(text)` | Copy text to clipboard |
| `UI.toggleFullscreen()` | Toggle browser fullscreen |

---

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `Esc` | Cancel active tool / close inspector / close dropdowns |
| `F11` | Toggle fullscreen |

---

## Basemaps

| Name | Provider | Best For |
|------|----------|----------|
| OpenStreetMap | OSM / openstreetmap.org | General-purpose mapping |
| ESRI Satellite | Esri World Imagery | Aerial/satellite context |
| CartoDB Dark | CARTO | Night mode, dark engineering aesthetic |
| OpenTopoMap | opentopomap.org | Terrain, elevation, topographic surveys |

---

## GIS Analysis Features

### Measurements (Turf.js powered)
- **Distance**: Click-to-add polyline. Double-click to finish. Displays total length in m / km.
- **Area**: Click polygon vertices. Double-click to close. Displays area (m², ha, km²) and perimeter.
- **Coordinate Picker**: Click any map point. Displays and auto-copies lat/lng to clipboard.

### Geometry Metrics (Inspector)
- Points: Latitude, Longitude, Elevation (if present)
- Lines: Length (m/km), vertex count
- Polygons: Area (m²/ha/km²), Perimeter (m/km), ring count, vertex count

### Bounding Extent
- Computes `[minLng, minLat, maxLng, maxLat]` across all visible layers (Turf bbox)
- Displays coordinates in the floating info card
- Draws a temporary extent rectangle on the map

---

## Performance Notes

| File Size | Expected Performance |
|-----------|----------------------|
| < 1 MB | Instant |
| 1–5 MB | < 2 seconds |
| 5–20 MB | 2–8 seconds |
| > 20 MB | May be slow; browser-limited |

For very large KML/KMZ files (>20 MB):
- Consider pre-simplifying geometries in QGIS before upload
- Use GeoJSON instead of KML for faster parsing
- Filter to only required features

---

## Offline / Air-gapped Operation

To run fully offline (no CDN):

1. Download the four library files:
   - `https://unpkg.com/leaflet@1.9.4/dist/leaflet.js`
   - `https://unpkg.com/leaflet@1.9.4/dist/leaflet.css`
   - `https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js`
   - `https://cdn.jsdelivr.net/npm/@tmcw/togeojson@5.8.1/dist/togeojson.umd.js`
   - `https://cdn.jsdelivr.net/npm/@turf/turf@6/turf.min.js`

2. Save them to `/libs/` alongside `index.html`

3. Update the `<link>` and `<script>` tags in `index.html` to point to local paths:
   ```html
   <link rel="stylesheet" href="libs/leaflet.css">
   <script src="libs/leaflet.js"></script>
   <script src="libs/jszip.min.js"></script>
   <script src="libs/togeojson.umd.js"></script>
   <script src="libs/turf.min.js"></script>
   ```

4. Note: basemap tiles (OpenStreetMap, ESRI Satellite, etc.) still require internet.  
   For offline basemaps, replace tile URLs with a local tile server (e.g. [MBTiles + tileserver-gl](https://github.com/maptiler/tileserver-gl)).

---

## Extending the Platform

### Adding a new basemap
```javascript
// In MapEngine.init(), add to App.basemapTiles:
App.basemapTiles.custom = L.tileLayer('https://your-tile-server/{z}/{x}/{y}.png', {
  attribution: 'Your Attribution',
  maxZoom: 18
});

// In MapEngine.switchBasemap(), add to labels:
const labels = { ..., custom: 'My Custom Basemap' };
```

### Adding a new file format
```javascript
// In FileEngine.processFile():
} else if (ext === 'csv') {
  const text = await this._readText(file);
  geojson = myCSVtoGeoJSON(text); // your custom parser
}
// Add 'csv' to FileEngine.SUPPORTED and to the <input accept> attribute
```

### Adding a new GIS tool
```javascript
// Add a button to the toolbar, then implement the tool following
// the MeasurementTools pattern: start() / stop() / click handler / result display
```

---

## Browser Compatibility

| Browser | Support |
|---------|---------|
| Chrome 90+ | ✅ Full support |
| Firefox 90+ | ✅ Full support |
| Edge 90+ | ✅ Full support |
| Safari 14+ | ✅ Full support |
| Mobile Chrome | ✅ Responsive |
| Mobile Safari | ✅ Responsive |

Requires: `FileReader API`, `DOMParser`, `Clipboard API`, `Fullscreen API`, `Fetch/Promise`.

---

## Design System

| Token | Value | Usage |
|-------|-------|-------|
| `--bg-base` | `#06090d` | App background |
| `--bg-panel` | `#0b1017` | Panel backgrounds |
| `--accent` | `#00d9c0` | Highlights, active states |
| `--warn` | `#f0900a` | Measurement drawing color |
| `--font-ui` | Barlow | UI text |
| `--font-mono` | JetBrains Mono | Coordinate data, attributes |

---

## License

MIT License

Copyright (c) 2025 GeoCart Surveys & Engineering

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

---

*GeoCart GIS Platform — Built for professional spatial workflows.*
