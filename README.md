# Scan to Order

A proof-of-concept web app that replaces a handheld scanner-gun ordering workflow with a phone.

Workers pick a **location**, scan item barcodes (phone camera, Bluetooth/USB wedge scanner, or manual entry), enter the **quantity to order**, and the app stores everything locally on the device grouped by location. When the scan run is complete, the app builds a text file and sends it out by email, the phone's share sheet, download, or copy-to-clipboard.

## Features

- **Barcode scanning** via the browser's built-in `BarcodeDetector` (Chrome/Android), with a ZXing fallback loaded from CDN for browsers without it (e.g. iOS Safari). Wedge scanners that type-and-press-Enter work out of the box through the manual entry field.
- **Quantity prompt** after every scan, with −/+ stepper and +5 / +10 / +100 quick-add buttons. Re-scanning an item lets you correct its quantity instead of duplicating it.
- **Offline-friendly local storage** — scans persist in `localStorage`, grouped by location, and survive closing the browser.
- **Configurable output format** — Settings lets you edit the file header, location header, and item line as templates with tokens (`{location}`, `{code}`, `{qty}`, `{date}`, `{count}`, `{units}`, …), with a live preview. Leave the location header blank for a flat one-line-per-item file.
- **Send / Export** — share as a real `.txt` attachment (Web Share API), open a pre-filled email (`mailto:`), download, or copy.

## Running it

It is a single self-contained file — `index.html` — with no build step and no dependencies (the ZXing fallback loads from a CDN only when needed).

- Open `index.html` directly, or serve it with anything (`python -m http.server`).
- **Camera scanning requires HTTPS** (browser security rule), so for phone testing host it on GitHub Pages, Netlify, or similar. Manual entry and wedge scanners work regardless.

## Default file format

```
# Order scan export 2026-07-06 17:48

[Aisle 3]
012345678905,4
44001122,1

[Backroom]
55009988,2
```

Fully editable in Settings → File format.

## Status

Proof of concept. Known follow-ups if it graduates:

- Upload to a real server endpoint instead of email.
- PWA manifest + service worker for install-to-home-screen and full offline use.
- Optional MSQ awareness (pre-fill quantity from a product list).
