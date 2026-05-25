# Britain Predicts — Iframe Files

## Files

| File | Purpose |
|------|---------|
| `iframe1_forecast_header.html` | Auto-run forecast header. Loads on page, runs STM silently, displays party tiles + majority call. "Create your own" button posts a postMessage to parent page to scroll to iframe 2. |
| `iframe2_calculator.html` | Interactive calculator. Pre-populated with current polling. User adjusts vote shares and runs model. Results displayed in same tile format as iframe 1. Broadcasts forecastUpdate postMessage to iframe 3. |
| `iframe3_map_list.html` | Constituency list + map. Left: scrollable list sorted by margin with defending-party colour strip. Right: choropleth map. Click to select and see dossier. Listens for forecastUpdate from iframe 2. |

## Deployment

1. Upload all three files to your `britainpredicts` GitHub Pages repo
2. Embed in your article page using iframes:

```html
<!-- Iframe 1: Forecast header -->
<iframe src="https://glasgow46239.github.io/britainpredicts/iframe1_forecast_header.html"
  width="100%" height="480" frameborder="0" scrolling="no"></iframe>

<!-- Your article text here -->

<!-- Iframe 2: Calculator -->
<iframe src="https://glasgow46239.github.io/britainpredicts/iframe2_calculator.html"
  id="calculator-iframe" width="100%" height="620" frameborder="0" scrolling="no"></iframe>

<!-- Iframe 3: Map + list -->
<iframe src="https://glasgow46239.github.io/britainpredicts/iframe3_map_list.html"
  width="100%" height="600" frameborder="0" scrolling="no"></iframe>
```

3. Add this script to your article page to handle the "Create your own" scroll:

```html
<script>
window.addEventListener('message', function(e) {
  if (e.data && e.data.action === 'scrollToCalculator') {
    document.getElementById('calculator-iframe').scrollIntoView({ behavior: 'smooth' });
  }
});
</script>
```

## GeoJSON
The map fetches: `https://raw.githubusercontent.com/glasgow46239/britainpredicts/main/parlcons_westmisnter.json`
This is already in your repo. The map will not render in local file:// preview — it requires the GitHub Pages hosted URL.

## Updating polling data
Edit `results_2024.csv` in the repo. Iframes 1 and 3 fetch live from GitHub on each page load.
The calculator (iframe 2) uses baked polling data for its initial state — update `POLL_NAT` and `POLL_REG` in the script if polling shifts significantly.

## Cross-iframe messaging
- Iframe 1 → Parent page: `{ action: 'scrollToCalculator' }`
- Iframe 2 → Parent page (and iframe 3 via parent relay): `{ action: 'forecastUpdate', counts: {...}, seats: [...] }`
- Iframe 3 listens for `forecastUpdate` and updates its map + list accordingly

## Northern Ireland
18 NI seats currently greyed out on the map with "projection coming soon" label.
Excluded from all seat totals with explicit GB-only scope note.
When ready to add NI projections, supply seat-level calls to Bobby for integration.
