# Safara Professional Golden Case

Status: draft

Case evaluasi pertama untuk Inference Engine. Case ini memakai `NormalizedDocument/v1` dan lima PNG hasil perception dari contoh Safara.

## Isi case

- `manifest.json` mengikat input, asset, dan expected output.
- `expected-output.json` berisi annotation/golden output draft untuk interpretation, entity, relation, source reference, dan evidence.

## Sumber input

Input tetap memakai file contoh yang ada di repository:

- `project's goal/example/ndv1-safara-professional-example.json`
- lima PNG `docling-visual-*.png` di folder yang sama

PDF asli hanya dicatat sebagai provenance. Runtime Inference Engine menerima `NormalizedDocument/v1`, bukan PDF mentah.

## Cakupan annotation

Case ini mencakup:

- interpretation dari text block dan table;
- extraction dari workflow, payment flow, document-review screen, dan readiness decision pada PNG;
- normalisasi angka pembayaran dan kuota;
- cross-modal relation antara hasil text/table dan hasil image;
- source reference berbasis `textBlock.id`, `table.id`, `visualRegion.id`, page, asset, dan bounding box;
- ambiguity pada data contoh yang hanya ditampilkan sebagai rancangan/UI.

Format output ini adalah `inference-evaluation-draft/v1`, bukan schema production final.
