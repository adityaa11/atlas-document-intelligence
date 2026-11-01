# Project Context: Inference Engine

- Status: ready
- Last reviewed: 2026-11-01
- Owner: user
- Approver: dapat didelegasikan oleh owner
- Accountable decision owner: user atau pihak yang didelegasikan

## Project frame

Project ini membangun Inference Engine generic dan multimodal untuk mengubah
hasil perception menjadi informasi semantic yang dapat digunakan oleh consumer
berikutnya.

Project memiliki lima tujuan semantic yang berurutan:

1. menghasilkan interpretation;
2. menghasilkan relation;
3. menghasilkan workflow;
4. menghasilkan facts;
5. menyiapkan data, index, dan API agar knowledge dapat di-retrieve oleh
   chatbot di luar scope project.

Interpretation dan relation merupakan primitive inference. Workflow dan facts
merupakan dua section output semantic utama. Keduanya dikelompokkan berdasarkan
makna, bukan urutan dokumen, dan dapat saling terhubung melalui relation
many-to-many.

Engine menerima `NormalizedDocument/v1` dan memproses:

- text block;
- table;
- PNG hasil perception;
- relasi antarhasil ekstraksi dari berbagai modality.

PDF mentah bukan input langsung Inference Engine. PDF diproses oleh perception
layer dan menghasilkan `NormalizedDocument/v1` beserta asset PNG yang
direferensikan melalui `visualRegions.assetRef`.

Inference Engine dipakai oleh background service dan hanya dapat dipanggil
oleh service yang memiliki identity terdaftar serta permission yang sesuai.
API contract bersifat identity-aware: identity pemanggil tidak dipercaya dari
payload request, tetapi dihasilkan dari proses autentikasi dan digunakan oleh
authorization policy serta audit.

Setiap hasil inference juga harus dapat ditelusuri kembali ke text, table, atau
image yang digunakan sebagai dasar hasil tersebut.

UI demo yang tersedia diperlakukan sebagai reference consumer untuk memahami
cara knowledge disajikan dan diakses. UI tersebut bukan deliverable Inference
Engine dan route frontend bukan bagian dari semantic contract engine.

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
- workflow group yang memiliki satu atau lebih detailed workflow;
- business result atau ringkasan hasil untuk workflow group bila dapat
  diturunkan dari sumber;
- fact group yang memiliki satu atau lebih fact;
- fact yang menyimpan original wording, interpretation, evidence, dan source
  reference;
- relation many-to-many antara workflow group, detailed workflow, dan fact;
- retrieval-ready representation yang mempertahankan evidence, provenance,
  approval state, dan hubungan knowledge.
- mendeteksi konflik antar-claim, interpretation, fact, atau workflow yang
  merujuk pada sumber berbeda;
- menandai konflik sebagai unresolved dan menyajikan seluruh claim yang
  bertentangan beserta evidence-nya untuk di-resolve oleh user.

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
- mendefinisikan schema workflow group dan detailed workflow;
- mendefinisikan schema fact group dan fact;
- mengelompokkan workflow dan fact berdasarkan makna;
- membangun relation many-to-many antara fact dan workflow;
- menyediakan projection, index, dan API untuk retrieval knowledge oleh
  consumer eksternal.
- mendeteksi dan menghasilkan conflict record untuk nilai, aturan, atau
  workflow yang tidak konsisten;
- mempertahankan source reference dari setiap claim yang terlibat dalam
  conflict.

### Out of scope

- membangun atau mengganti perception engine;
- menerima atau memproses PDF mentah secara langsung;
- mengganti schema `NormalizedDocument/v1`;
- mengunci engine pada domain Safara;
- membangun chatbot atau conversational UI;
- membangun atau mengganti UI demo/reference consumer;
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

Output utama yang dikonsumsi oleh sistem berikutnya dikelompokkan menjadi:

- workflow groups, yang berisi detailed workflow dan dapat memiliki business
  result;
- fact groups, yang berisi fact dengan original wording, interpretation,
  evidence, dan source reference.

Workflow group, detailed workflow, dan fact dapat memiliki relation
many-to-many. Navigasi knowledge harus dapat berjalan dari workflow ke fact dan
dari fact ke workflow.

Retrieval boundary mencakup data canonical, index, dan API yang diperlukan
consumer eksternal untuk menemukan knowledge yang grounded. Chatbot yang
menggunakan retrieval tersebut berada di luar scope project.

Conflict boundary mencakup deteksi, penjelasan, evidence, dan status unresolved
untuk konflik semantic. Engine tidak mengambil keputusan final untuk memilih
claim yang benar; resolution dilakukan oleh user atau authority yang ditunjuk.

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

## Identity-aware access contract

Runtime Inference Engine menyediakan API HTTP berbasis Fastify untuk background
service. Target production menggunakan HTTPS dengan mTLS. Authentication dan
authorization merupakan tahap terpisah:

- authentication menentukan identity service yang memanggil;
- authorization menentukan apakah identity tersebut boleh menjalankan
  inference;
- audit mencatat identity, keputusan akses, dan keterkaitan request atau job.

Pada development, setiap background service menggunakan credential terpisah.
Credential menghasilkan principal dan scope yang sama dengan yang akan
digunakan pada production, tetapi mekanisme autentikasinya bersifat
development-only dan dapat diganti tanpa mengubah API contract atau policy.

Target production adalah mutual TLS (mTLS) antarservice. Certificate identity
dipetakan ke principal service, kemudian authorization policy tetap ditegakkan
di Fastify sebelum inference dijalankan.

Identity context minimal mencakup:

- `principal` atau service identity;
- scope atau permission;
- authentication method;
- `requestId` dan, bila asynchronous, `jobId`;
- initiating principal dan executing principal bila keduanya berbeda.

Identity context harus berasal dari credential atau certificate yang telah
diverifikasi. Client tidak boleh menetapkan principal melalui payload request.

Setiap request yang gagal authentication atau authorization harus ditolak
sebelum inference dijalankan. Audit log wajib mencatat request berhasil,
request ditolak, principal, authentication method, keputusan authorization,
request atau job ID, waktu, dan policy yang digunakan. Credential, private key,
dan isi dokumen sensitif tidak boleh dicatat ke audit log.

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
- Belum ada implementasi identity-aware access contract untuk background
  service.
- Belum ada schema atau pipeline untuk workflow group dan detailed workflow.
- Belum ada schema atau pipeline untuk fact group dan fact.
- Belum ada relation many-to-many yang menghubungkan fact dengan workflow.
- UI reference menunjukkan section workflow dan facts yang terkelompok, source
  evidence, business result, serta akses ke workflow yang terkait; kontrak
  backend yang menghasilkan knowledge tersebut belum menjadi contract engine.
- Belum ada retrieval-ready canonical representation, index, atau API untuk
  knowledge semantic.
- Belum ada schema atau pipeline untuk conflict detection dan unresolved
  conflict record.

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
- Background service dapat memanggil engine melalui API contract yang
  identity-aware.
- Development menggunakan credential terpisah per service.
- Production menggunakan mTLS sebagai target autentikasi antarservice.
- Authorization policy ditegakkan di Fastify sebelum inference dijalankan.
- Setiap request atau job dapat ditelusuri melalui audit log wajib.
- Tersedia workflow group dengan detailed workflow yang dapat diakses sebagai
  unit knowledge terkelompok.
- Tersedia fact group dengan fact yang menyimpan original wording,
  interpretation, evidence, source reference, dan approval state.
- Fact dan workflow terhubung secara many-to-many dan dapat dinavigasi dua arah.
- Tersedia business result untuk workflow group bila didukung evidence.
- Tersedia retrieval-ready data, index, dan API yang mengembalikan knowledge
  beserta evidence, provenance, dan approval state.
- Konflik antar-claim atau antar-sumber dapat dideteksi, ditampilkan, dan
  ditelusuri ke evidence masing-masing.
- Conflict record tetap unresolved sampai user atau authority yang ditunjuk
  melakukan resolution.

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
- Grouping workflow dan fact harus berdasarkan makna semantic, bukan hanya
  urutan atau posisi dalam dokumen.
- Setiap workflow group, detailed workflow, fact group, dan fact harus
  memiliki identifier stabil agar relation dan retrieval dapat direproduksi.
- Relation fact-workflow bersifat many-to-many; implementasi tidak boleh
  mengasumsikan satu fact hanya dimiliki satu workflow.
- Retrieval result tidak boleh kehilangan source reference, evidence,
  provenance, atau approval state.
- Konflik tidak boleh diselesaikan secara diam-diam dengan memilih salah satu
  claim tanpa keputusan user atau policy yang telah disetujui.
- Semua claim yang terlibat dalam conflict harus tetap dapat ditelusuri ke
  sumber dan evidence masing-masing.
- Reproducibility berlaku untuk input, schema, asset, dan aturan inference
  yang sama.
- Kualitas hasil perception dapat memengaruhi hasil image extraction dan
  validitas evidence.
- Threshold numerik belum ditentukan.
- Credential development harus dikelola sebagai secret dan tidak boleh menjadi
  credential global untuk semua service.
- Mekanisme authentication development dan production harus menghasilkan
  identity context, principal, dan permission semantics yang kompatibel.
- Perubahan dari credential development ke mTLS tidak boleh mengubah
  authorization policy atau API contract inti.
- Fastify harus menolak request tanpa identity valid atau permission yang
  sesuai sebelum inference dijalankan.
- Audit identity dan keputusan authorization merupakan invariant project.

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
- background service yang tidak terautentikasi atau tidak terotorisasi tidak
  dapat menjalankan inference;
- identity pemrakarsa dan pelaksana dapat ditelusuri untuk request atau job;
- perpindahan dari credential development ke mTLS production tidak mengubah
  API contract dan authorization semantics inti;
- audit log authentication, authorization, dan execution tersedia untuk
  review.
- workflow group dan fact group terbentuk secara koheren dari expected output;
- detailed workflow berada pada group yang benar;
- fact berada pada group yang benar;
- relation many-to-many antara fact dan workflow lengkap dan tidak
  menghubungkan knowledge secara keliru;
- pengguna consumer dapat menelusuri workflow ke fact dan fact ke workflow;
- retrieval API dapat mengembalikan knowledge yang relevan serta evidence dan
  source reference yang mendasarinya;
- data yang belum disetujui atau ambigu tetap ditandai dan tidak disajikan
  sebagai knowledge final secara diam-diam.
- konflik nilai, aturan, atau workflow ditandai sebagai conflict dan tidak
  disajikan sebagai satu knowledge final yang seolah-olah sudah benar;
- user dapat melihat claim yang bertentangan, sumbernya, evidence-nya, dan
  status resolution;
- resolution yang dipilih user dapat dipakai untuk memperbarui knowledge dan
  retrieval result secara traceable.

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
- Runtime deployment menggunakan Docker Compose dan API HTTP berbasis Fastify;
  production menggunakan HTTPS dengan mTLS.
- API contract bersifat identity-aware dan tidak mempercayai identity dari
  payload request.
- Development menggunakan credential terpisah per background service.
- mTLS adalah target production untuk authentication antarservice.
- Authorization policy ditegakkan di Fastify sebelum inference dijalankan.
- Audit log authentication, authorization, dan execution bersifat wajib.
- Lima tujuan semantic project adalah interpretation, relation, workflow, facts,
  dan retrieval-ready knowledge.
- Workflow dan facts merupakan output semantic utama yang dikelompokkan.
- Workflow group dapat memiliki banyak detailed workflow.
- Fact group dapat memiliki banyak fact.
- Fact dan workflow memiliki relation many-to-many serta dapat diakses dua arah.
- Fact menyimpan original wording, interpretation, evidence, dan source
  reference.
- Retrieval-ready data, index, dan API termasuk scope project.
- Chatbot implementation dan conversational UI berada di luar scope project.
- UI demo/reference consumer dipakai sebagai acuan penyajian dan akses
  knowledge, bukan sebagai deliverable engine.
- Conflict detection dan unresolved conflict marking termasuk tujuan semantic
  project.
- Engine harus mempertahankan claim yang bertentangan dan evidence masing-masing
  sampai user atau authority yang ditunjuk menyelesaikan conflict.

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
- Detail format credential development dan mekanisme penyimpanannya.
- Private CA, certificate issuance, renewal, dan rotation policy untuk mTLS.
- Detail mapping certificate identity ke principal service.
- Granularity final untuk scope, role, tenant, dan policy authorization.
- Format audit log, retention, dan sistem penyimpanannya.
- Detail final schema workflow group, detailed workflow, fact group, dan fact.
- Detail algoritme atau aturan grouping semantic.
- Detail vocabulary dan cardinality final untuk relation fact-workflow.
- Detail retrieval index, chunking, ranking, filtering, dan freshness policy.
- Detail approval lifecycle dan perubahan status knowledge.
- Detail conflict schema, conflict severity, dan conflict lifecycle.
- Detail precedence atau resolution policy apabila user memilih untuk
  mengotomatisasi sebagian resolution.

## Risks and dependencies

- Variasi kualitas dan struktur PNG dapat memengaruhi image extraction.
- `assetRef` yang tidak dapat diakses akan menghambat image inference.
- Source reference yang tidak stabil akan menghambat audit dan reproducibility.
- Expected output yang belum distandardisasi dapat menyulitkan evaluasi.
- Variasi `NormalizedDocument/v1` dari PDF berbeda dapat memengaruhi
  generalisasi.
- Perubahan versi schema dapat memengaruhi kompatibilitas.
- Evidence perception yang tidak lengkap dapat membatasi validasi hasil.
- Pengelompokan yang salah dapat membuat workflow atau fact terlihat koheren
  tetapi tidak sesuai makna sumber.
- Konflik antar-PRD dapat menghasilkan fact yang bertentangan dan memerlukan
  approval atau resolution policy.
- Relation many-to-many yang terlalu luas dapat menghasilkan retrieval yang
  tidak relevan atau sulit dijelaskan.
- Index retrieval yang tidak disinkronkan dengan approval atau perubahan fact
  dapat menyajikan knowledge yang stale atau belum disetujui.
- Konflik yang tidak terdeteksi dapat menghasilkan workflow atau fact yang
  salah tetapi terlihat meyakinkan.
- Konflik yang ditandai tetapi tidak ikut masuk ke retrieval context dapat
  menyebabkan chatbot consumer menerima knowledge yang tidak lengkap.
- Credential per-service yang bocor dapat memungkinkan pemanggilan inference
  tanpa izin sampai credential dicabut atau diganti.
- Certificate expiry atau kegagalan rotation dapat menghentikan komunikasi
  production antarservice.
- Perbedaan identity mapping antara development credential dan mTLS dapat
  menyebabkan policy tidak konsisten saat deployment.

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
- Deployment runtime menggunakan Docker Compose dan komunikasi HTTP Fastify.
- Background service memakai per-service credential pada development.
- Target production menggunakan mTLS.
- Fastify menegakkan authorization policy.
- Audit log authentication dan authorization wajib.

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
- Detail format API contract identity-aware.
- Detail credential development, private CA, certificate lifecycle, dan
  authorization policy final.
