# Scan to Order

A proof-of-concept web app that replaces a handheld scanner-gun ordering workflow with a phone.

Workers pick a **location**, scan item barcodes (phone camera, Bluetooth/USB wedge scanner, or manual entry), enter the **quantity to order**, and the app stores everything locally on the device grouped by location. When the scan run is complete, the app builds a text file and sends it out by email, the phone's share sheet, download, or copy-to-clipboard.

## Features

- **Barcode scanning** via the browser's built-in `BarcodeDetector` (Chrome/Android), with a vendored ZXing fallback for browsers without a working detector (iOS Safari, desktop Chrome). Wedge scanners that type-and-press-Enter work out of the box through the manual entry field.
- **Continuous scanning** — the camera stays live between scans; the quantity prompt overlays the viewfinder and saving returns you straight to scanning. A flashlight toggle appears on devices that support it.
- **Quantity prompt** after every scan, with −/+ stepper and +5 / +10 / +100 quick-add buttons. Re-scanning an item lets you correct its quantity instead of duplicating it; scanning a new item while the prompt is open auto-saves the pending entry, and dismissing the prompt offers Undo.
- **Offline-friendly local storage** — scans persist in `localStorage`, grouped by location (rename/delete/merge supported), and survive closing the browser. A service worker caches the app shell, so it loads and scans with no signal once visited.
- **Installable PWA** — manifest + icons; "Add to Home Screen" gives an app icon and exempts the stored data from Safari's 7-day eviction.
- **Configurable output format** — Settings lets you edit the file header, location header, and item line as templates with tokens (`{location}`, `{code}`, `{qty}`, `{date}`, `{count}`, `{units}`, …), with a live preview. Leave the location header blank for a flat one-line-per-item file.
- **Send / Export** — share as a real `.txt` attachment (Web Share API), open a pre-filled email (`mailto:`), download, or copy.

## Running it

No build step: `index.html` plus a vendored scanner library (`zxing-browser.min.js`), a PWA manifest, a service worker (`sw.js`), and icons.

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
- Optional MSQ awareness (pre-fill quantity from a product list).
- Quoting/escaping for codes containing the item-line delimiter (QR payloads with commas).
- Full modal focus trap for screen-reader users (Escape-to-close and dialog roles are in).
