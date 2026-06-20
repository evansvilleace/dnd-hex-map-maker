Live site available at: https://evansvilleace.github.io/dnd-hex-map-maker/index.html

Description
A single-file, no-build-step browser tool for overlaying a hex grid on a map and exporting print-ready PNGs, built for tabletop RPG region/travel planning at multiple real-world scales (1, 6, 60, 360, and 3600 miles per hex).

Open hex-map-exporter.html directly in a browser, or serve the folder with any static file server. No installation, no dependencies to manage, no server-side code.

Files


hex-map-exporter.html — the entire application: UI, rendering, and export logic in one file.
world-land.svg — the bundled world map (land, lakes, and major rivers), built from Natural Earth 1:10m data. Must stay in the same folder as the HTML file; it's loaded at startup as the default map source.


Both files are required together. If you only grab the HTML file, the bundled world map won't load (you can still use the "Upload your own map" option without it).

Running it

Quickest: double-click hex-map-exporter.html to open it directly in a browser (file://). This works for everyday use, with one caveat below.

For full functionality: serve the folder with a local static server, e.g.:

python3 -m http.server 8000

then visit http://localhost:8000/hex-map-exporter.html. This also works if you host the folder on GitHub Pages or similar.

Why does that matter?

Browsers restrict what local files (file://) are allowed to do for security reasons. Specifically:


Exporting the bundled world map as PNG requires http(s)://. Opening the file directly blocks this one operation; the app will tell you so if you try and explain the same fix (run a local server, or host it). Uploading your own custom map file works fine either way, since that goes through a different loading path that isn't affected.
Everything else — panning, zooming, switching hex scales, the live OSM tile view, uploading your own map — works identically under either method.


Using the tool


Pick a map source. The bundled world map loads automatically. Use "Upload your own map" to load a custom image or SVG instead (e.g. a hand-drawn regional map), or toggle to "Live tiles (OSM)" for real-world roads and place names via OpenStreetMap data.
Set the map's real-world width, in miles, via the Width field. For the bundled world map this is pre-filled at 24,901 mi (Earth's equatorial circumference); for a custom upload, set it to whatever real-world distance the image's full width represents.
Toggle one or more hex scales (1 / 6 / 60 / 360 / 3600 mi) — multiple can be active at once, each in its own color.
Pan and zoom to frame the region you want (drag to pan, scroll/pinch to zoom, or use the Zoom slider). Use Fit view to reset to the full map.
Align the grid if needed with the nudge arrows (or arrow keys) — useful for lining hexes up with a specific reference point.
Export PNG to download print-ready 300 DPI images. Check "Separate layers" to export the map and the hex grid as two separate transparent-background PNGs (handy for layering in Photoshop or similar), or leave it unchecked for one combined image.


Live tiles (OSM) mode

Switching to "Live tiles (OSM)" replaces the bundled map with live, pannable map tiles (via CartoDB's basemap, built on OpenStreetMap data) showing real roads, cities, and place names — useful when you want to ground a session in recognizable, current-day geography rather than the stylized bundled map.

Export is disabled in this mode. Live map tiles are cross-origin and can't be reliably exported from a canvas due to browser security restrictions and the tile provider's usage terms — this is a hard constraint of using live tiles at all, not a bug. If you need an image of what you're seeing, use your OS or browser's screenshot tool instead; the app will remind you of this when the export button is disabled.

The hex grid behaves identically in both modes — same scales, same colors, same alignment controls — just computed two different ways under the hood (the bundled map uses flat pixel math with a latitude correction; live tiles use Leaflet's own real-world projection).

Custom maps

Uploading your own image or SVG works the same way as the bundled map, with one difference: there's no defined real-world projection for an arbitrary uploaded image, so the hex grid uses a simple flat width-to-miles ratio rather than the latitude-aware correction applied to the bundled world map (see below). For a single-region map (a city, a province, a fictional continent) this distinction won't matter in practice — it primarily affects accuracy across large spans of latitude on a world-scale map.

Notes on accuracy

The bundled world map is a plain equirectangular projection (longitude maps linearly to x, latitude linearly to y) — simple to build and render, but it means a fixed number of pixels does not represent a fixed real-world distance at every latitude. Real distance per degree of longitude shrinks the closer you get to the poles. The app corrects for this automatically by computing hex size relative to the latitude you're currently viewing, so a "360 mi" hex represents close to 360 real miles whether you're looking at the equator or northern Canada — but this also means hex size on screen will visibly grow or shrink somewhat as you pan vertically across latitudes, which is expected behavior on a flat projection of a sphere, not a bug.

Browser support

Built and tested in Chromium-based browsers. Uses standard Canvas 2D, no exotic APIs — should work in any modern evergreen browser, but hasn't been specifically tested in Safari or Firefox.
