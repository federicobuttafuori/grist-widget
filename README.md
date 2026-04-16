# Grist Label Designer (Custom Label)

Single-page **label designer** for [Grist](https://www.getgrist.com/): canvas-based layouts, profiles, barcodes/QR, export, and optional **Grist table** integration so row data can drive fields on the label.

## File

| File | Purpose |
|------|---------|
| `index.html` | Full app (UI, canvas, Grist hooks, persistence) |

## Running it

- **Inside Grist**: add a **Custom widget**, paste or upload `index.html` (or host the file and point the widget at its URL, depending on your Grist setup).
- **Standalone**: open `index.html` in a browser for local editing; Grist features activate when the Grist plugin API is present.

## Grist integration

- Uses `grist-plugin-api.js` and `grist.ready` with appropriate access.
- `grist.onRecord` feeds the active row into the designer (column catalog, mapped fields, render pipeline).
- `grist.onOptions` is registered (no-op handler) so the host can wire options if needed.

## Persistence

- **Local state** (profiles, designs, zoom, and related data) is stored in **`localStorage`** under an app-specific key, so work survives reloads and custom-widget-builder paste cycles even when `setOptions` is unreliable.

## Features (high level)

- **Profiles** and multiple **designs** per profile (sizes in cm, canvas per design).
- **Elements**: text, images, barcodes (JsBarcode), QR codes (qrcodejs), shapes, etc. (see in-app).
- **Properties** panel with sync/override patterns for Grist-mapped fields.
- **Export** via html2canvas (and related flows in the UI).
- **Regex rules** and batch behaviors where implemented in the sidebar.

## Requirements

- Modern browser with canvas, `localStorage`, and ES features used by the bundle.
- **CDN scripts** loaded in the page header: Grist plugin API, JsBarcode, QRCode, html2canvas, Google Fonts (see `index.html` head).

## Related projects

- **Grist Wide Pie Widget** — sibling folder `Grist Wide Pie Widget/` (wide-row pie chart custom widget; see its `README.md`).
