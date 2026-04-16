# Grist Label Designer Widget

**What problem it solves:** Labels must stay tied to **live row data** in Grist (SKU, barcodes, text) while meeting **print and layout** constraints. A spreadsheet is not a label designer. This app provides a **canvas editor with column bindings** and export/print flows so one template can render many physical labels per record.

A custom **Grist widget** for building and printing product labels directly from table data.

This project is a single-page widget (`index.html`) that provides a visual editor with drag-and-drop elements, data binding, synchronized content blocks, import/export, and print-focused output (including thermal black-and-white preview controls).

## Highlights

- Visual canvas editor with zoom and multi-select interactions.
- Element types:
  - Text
  - Data field bindings (from Grist columns)
  - Images
  - QR codes
  - Barcodes
  - Lines and boxes
- Label profiles/sizes and multiple designs per profile.
- Two-canvas workflow support (Canvas 2 can be enabled).
- Synchronized blocks to keep content-related properties aligned across elements.
- Global regex transformation rules for text rendering.
- Layout export/import and snapshot export/import.
- Print actions for full batch and single-label output.
- Thermal-oriented black & white preview tuning (gamma/contrast/threshold controls).

## Project Structure

- `index.html` – Main widget app (UI, editor logic, Grist integration, print pipeline).
- `lib/qrcode.min.js` – Local QR utility dependency.
- `docs/debug-learnings.md` – Notes from debugging print-outline behavior and final SVG-filter approach.

## Requirements

- A Grist document where this custom widget is embedded.
- Browser with JavaScript enabled.
- Network access for CDN dependencies referenced by `index.html`:
  - `grist-plugin-api.js`
  - `JsBarcode`
  - `qrcodejs` (CDN reference in page)
  - `html2canvas`
  - Google Fonts

## Usage in Grist

1. In Grist, add a **Custom Widget**.
2. Host this repository content (or at least `index.html`) on a reachable URL.
3. Point the widget URL to the hosted `index.html`.
4. Connect the widget to your table and configure access as needed.
5. In the widget:
   - Choose or create a label profile/design.
   - Add elements and bind data fields to columns.
   - Configure properties in the sidebar.
   - Use Print / Print Single when ready.

## Local Development

Because this is a static page, you can run it with a simple local server:

```bash
python3 -m http.server 8000
```

Then open:

- `http://localhost:8000/index.html`

> Note: Grist-specific APIs only work fully when loaded as an actual Grist custom widget.

## Printing Notes

The widget includes a print-oriented contour/outline solution based on SVG filters (instead of relying only on CSS shadows), improving reliability for monochrome/thermal scenarios. See `docs/debug-learnings.md` for implementation notes and rationale.

## License

No license file is currently included in this repository. Add one if distribution terms are required.
