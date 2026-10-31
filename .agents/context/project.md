# Project Context: Inference Engine

- Status: ready
- Last reviewed: 2026-10-31
- Owner: user
- Approver: dapat didelegasikan oleh owner
- Accountable decision owner: user atau pihak yang didelegasikan

## Project frame

Project ini membangun Inference Engine generic dan multimodal untuk mengubah
hasil perception menjadi informasi semantic yang dapat digunakan oleh consumer
berikutnya.

Engine menerima `NormalizedDocument/v1` dan memproses:

- text block;
- table;
- PNG hasil perception;
- relasi antarhasil ekstraksi dari berbagai modality.

PDF mentah bukan input langsung Inference Engine. PDF diproses oleh perception
layer dan menghasilkan `NormalizedDocument/v1` beserta asset PNG yang
direferensikan melalui `visualRegions.assetRef`.

Setiap hasil inference juga harus dapat ditelusuri kembali ke text, table, atau
image yang digunakan sebagai dasar hasil tersebut.

## Objectives and intended outcomes

Inference Engine harus menghasilkan:

- interpretasi entity dan field;
- ekstraksi data dari text, table, dan image;
- normalisasi nilai;
- relasi antar-entity;
- relasi antara data dari image dengan data dari text atau table;
- source reference untuk setiap hasil inference;
- evidence locator;
- confidence;
- ambiguity atau unresolved interpretation.

Source reference harus dapat menunjukkan sumber yang digunakan, seperti:

- `textBlock.id` dan nomor halaman;
- `table.id` dan nomor halaman;
- `visualRegion.id`;
- `assetRef` PNG;
- bounding box atau area image bila tersedia.

Satu hasil interpretation atau relation dapat memiliki lebih dari satu source
reference, misalnya gabungan text block dan image.

Engine harus dapat digunakan untuk berbagai domain dan berbagai hasil
`NormalizedDocument/v1`, bukan hanya domain Safara.

## Scope

### In scope

- mendefinisikan schema/kontrak output interpretation;
- mendefinisikan schema/kontrak output relation;
- mendefinisikan representasi data hasil ekstraksi image;
- mendefinisikan schema source reference dan evidence;
- mengekstrak data dari PNG yang dihasilkan perception;
- menghubungkan hasil image dengan hasil text, table, atau image lain;
- mencatat text, table, dan image yang digunakan oleh setiap output inference;
- menyediakan traceability untuk interpretation, image extraction, dan relation;
- menentukan evidence, confidence, dan ambiguity;
- mengevaluasi hasil terhadap expected output;
- menjaga reproducibility lintas berbagai input `NormalizedDocument/v1`.

### Out of scope

- membangun atau mengganti perception engine;
- menerima atau memproses PDF mentah secara langsung;
- mengganti schema `NormalizedDocument/v1`;
- mengunci engine pada domain Safara;
- menetapkan package, framework, atau arsitektur implementasi.

## Input and system boundary

### Runtime input

Input utama adalah objek `NormalizedDocument/v1`.

Input tersebut dapat berisi:

- pages;
- text blocks;
- tables;
- visual regions;
- metadata provenance;
- execution dan source fingerprints.

Jika dokumen memiliki gambar, perception menghasilkan PNG. PNG tersebut
direferensikan oleh `visualRegions.assetRef`, misalnya:

```text
derived/4ed9d136-d6e1-431f-97f0-c7167a5a35a3
```

Inference Engine menggunakan referensi tersebut untuk mengakses dan
mengekstrak data dari image asset.

### Output boundary

Output semantic mencakup:

- interpretation;
- relation;
- image-derived data;
- cross-modal relation;
- source reference;
- evidence;
- confidence;
- ambiguity.

Setiap interpretation dan relation harus memiliki source reference yang
menjelaskan text, table, image, atau kombinasi sumber yang digunakan.

Source reference harus memungkinkan hasil inference:

- diaudit;
- diverifikasi;
- ditelusuri kembali ke input;
- direproduksi dalam kondisi input dan aturan yang sama.

Schema output interpretation, relation, dan source reference belum tersedia
pada evidence repository dan menjadi bagian dari project ini.

`REL` belum tersedia sebagai graph pada `NormalizedDocument/v1`. Relation
merupakan output baru dari Inference Engine.

## Current and target state

### Current state

- `NormalizedDocument/v1` tersedia sebagai schema input.
- `visualRegions` dapat merujuk ke asset PNG hasil perception.
- Contoh hasil perception Safara tersedia.
- Schema export mendefinisikan `NormalizedDocument/v1` dan
  `SemanticBatchPlanInput/v1`.
- Belum ada schema output khusus untuk interpretation dan relation.
- Belum ada output semantic multimodal yang menghubungkan image dengan text
  atau table.
- Belum ada kontrak source reference yang menghubungkan setiap output dengan
  sumber input yang digunakan.

### Target state

- Tersedia schema output interpretation yang generic dan versioned.
- Tersedia schema output relation yang generic dan versioned.
- Tersedia schema source reference dan evidence yang generic dan versioned.
- Inference Engine memproses text, table, dan PNG hasil perception.
- Data dari image dapat dihubungkan dengan data dari text, table, atau image
  lain.
- Setiap interpretation dan relation memiliki source reference.
- Evidence dapat menunjuk ke page, text block, table, visual region, asset PNG,
  dan area image bila tersedia.
- Hasil menyatakan confidence dan ambiguity bila diperlukan.
- Hasil reproducible pada berbagai `NormalizedDocument/v1` dari PDF berbeda.

## People and governance

Owner project adalah user.

Owner dapat mendelegasikan:

- approver;
- pengambil keputusan utama;
- reviewer expected output;
- validator hasil evaluasi.

Role spesifik penerima delegasi belum ditentukan dan bersifat `to-confirm`.

## Constraints and assumptions

- Input harus sesuai kontrak `NormalizedDocument/v1`.
- PNG hasil perception harus dapat diakses melalui `visualRegions.assetRef`.
- Engine tidak boleh mengasumsikan semua input berasal dari domain Safara.
- Schema output harus mendukung versioning.
- Source reference harus dapat menunjuk ke sumber yang tersedia dalam
  `NormalizedDocument/v1` atau asset perception terkait.
- Evidence harus dapat menunjuk ke page, text block, table, visual region,
  asset PNG, dan area image bila tersedia.
- Satu output dapat menggunakan banyak source reference.
- Reproducibility berlaku untuk input, schema, asset, dan aturan inference
  yang sama.
- Kualitas hasil perception dapat memengaruhi hasil image extraction dan
  validitas evidence.
- Threshold numerik belum ditentukan.

## Success and acceptance basis

Project dianggap berhasil apabila:

- interpretation akurat terhadap expected output;
- data yang diekstrak dari image akurat;
- relasi antar-entity lengkap;
- relasi cross-modal antara image, text, dan table benar;
- setiap interpretation dapat ditelusuri ke sumber yang digunakan;
- setiap relation memiliki evidence yang menjelaskan dasar hubungan tersebut;
- source reference valid dan dapat digunakan untuk audit atau review;
- evidence yang disertakan valid dan dapat ditelusuri;
- confidence dan ambiguity tersedia saat dibutuhkan;
- hasil reproducible untuk berbagai `NormalizedDocument/v1` dari PDF berbeda.

Expected output dapat berasal dari:

- dataset berlabel atau golden output;
- review manual oleh user atau reviewer yang didelegasikan;
- sumber evaluasi lain yang disepakati.

Tidak semua metode evaluasi wajib digunakan sekaligus.

## Confirmed decisions

- Inference Engine bersifat generic.
- Inference Engine bersifat multimodal.
- Input runtime adalah `NormalizedDocument/v1`.
- PNG hasil perception diakses melalui `visualRegions.assetRef`.
- PDF bukan input langsung Inference Engine.
- Image extraction termasuk scope project.
- Cross-modal relation termasuk scope project.
- Setiap output inference harus mencatat sumber yang digunakan.
- Source reference dapat menunjuk ke text, table, image, atau kombinasi sumber.
- Project mencakup pendefinisian schema output interpretation.
- Project mencakup pendefinisian schema output relation.
- Project mencakup pendefinisian schema source reference dan evidence.
- Owner adalah user dan dapat mendelegasikan authority.
- Kriteria sukses mencakup akurasi, kelengkapan relasi, evidence valid,
  traceability, dan reproducibility.

## Deferred decisions

- Detail final schema output interpretation.
- Detail final schema output relation.
- Detail format output image-derived data.
- Detail representasi cross-modal relation.
- Detail nama field dan format source reference.
- Detail granularity evidence untuk area image.
- Threshold numerik untuk akurasi dan kelengkapan.
- Dataset evaluasi final.
- Role approver atau decision owner yang didelegasikan.
- Kebijakan versioning dan compatibility output.

## Risks and dependencies

- Variasi kualitas dan struktur PNG dapat memengaruhi image extraction.
- `assetRef` yang tidak dapat diakses akan menghambat image inference.
- Source reference yang tidak stabil akan menghambat audit dan reproducibility.
- Expected output yang belum distandardisasi dapat menyulitkan evaluasi.
- Variasi `NormalizedDocument/v1` dari PDF berbeda dapat memengaruhi
  generalisasi.
- Perubahan versi schema dapat memengaruhi kompatibilitas.
- Evidence perception yang tidak lengkap dapat membatasi validasi hasil.

## Evidence and claim states

### User intent

- Project membangun Inference Engine generic dan multimodal.
- Input engine adalah `NormalizedDocument/v1`.
- PNG hasil perception menjadi input pendukung melalui `assetRef`.
- Engine mengekstrak data dari image.
- Engine menentukan interpretation dan relation cross-modal.
- Setiap output mencatat text, table, atau image yang digunakan.
- PDF bukan input langsung.
- User adalah owner dan dapat mendelegasikan authority.
- Kriteria sukses mencakup akurasi, kelengkapan relasi, evidence valid,
  traceability, dan reproducibility.

### Observed or documented

- `NormalizedDocument/v1` memiliki `pages`, `textBlocks`, `tables`, dan
  `visualRegions`.
- `visualRegions` memiliki `assetRef` ke asset turunan.
- Contoh perception menghasilkan PNG asset untuk visual region.
- `SemanticBatchPlanInput/v1` membawa reference dan integrity fields.
- REL graph tidak tersedia pada `NormalizedDocument/v1`.

Evidence:

- [NDV1 schema export](<../../project's goal/example/ndv1-schema-export.json>)
- [NDV1 Safara example](<../../project's goal/example/ndv1-safara-professional-example.json>)
- [Safara source PDF](<../../project's goal/example/Safara_Buyer_Business_PRD_Professional.pdf>)

### Inferred

- Schema output perlu generic dan versioned.
- Reproducibility membutuhkan input, schema, asset, dan aturan inference yang
  sama.
- Asset PNG perlu tetap tersedia selama proses inference dan evaluasi.
- Source reference perlu menjadi bagian dari setiap hasil interpretation dan
  relation.

### To-confirm

- Approver atau decision owner yang didelegasikan.
- Threshold numerik dan dataset evaluasi final.
- Detail schema output interpretation dan relation.
- Detail format cross-modal relation.
- Detail nama field dan granularity source reference.
