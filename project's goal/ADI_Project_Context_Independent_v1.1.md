# ATLAS DOCUMENT INTELLIGENCE (ADI)
## Project Context — v1.1 · Independent Baseline

**Status:** Initial Project Planning  
**Project:** Atlas Document Intelligence (ADI)  
**Repository:** `adityaa11/atlas-document-intelligence`  
**Development branch:** `worker1`  
**Primary language:** Python  
**Development environment:** Windows, VS Code, project-local Python virtual environment  
**Document role:** Defines ADI's purpose, scope, principles, research direction, and success criteria.

> **Project independence:** This document is authored from the perspective of ADI as its own project. ADI's design decisions are governed by its own requirements and verified experiments. No other application's internal architecture, repository history, database schema, or implementation status is assumed to be part of ADI's project context.

---

# 1. Project Vision

Atlas Document Intelligence adalah proyek AI Engineering yang bertujuan membangun kemampuan **pemahaman semantik dokumen** melalui eksperimen yang terukur dan implementasi bertahap.

ADI mempelajari informasi dari dokumen yang dapat berisi:

- Teks, paragraf, heading, dan struktur bagian.
- Tabel dan data semi-terstruktur.
- Gambar, screenshot, grafik, dan diagram.
- Referensi silang serta informasi yang tersebar di beberapa bagian atau halaman.
- Pernyataan bersyarat, pengecualian, negasi, dan ambiguitas.

Tujuan ADI bukan sekadar merangkum isi dokumen atau menemukan kata kunci. ADI berupaya **menginterpretasikan makna**, mengekstraksi informasi terstruktur, dan mengaitkan setiap interpretasi dengan bukti yang dapat diperiksa.

## 1.1 Primary Objective

Membangun **model-agnostic, evidence-grounded document semantic inference engine** yang secara bertahap mampu:

1. Menerima dan memvalidasi representasi dokumen yang didukung ADI.
2. Menyusun konteks dari elemen dokumen, baik tekstual maupun visual.
3. Mengidentifikasi entitas, peristiwa, aturan, kondisi, dan relasi semantik.
4. Menginterpretasikan hubungan lintas bagian dan lintas modalitas ketika didukung bukti.
5. Menghubungkan setiap hasil dengan lokasi atau elemen sumber yang relevan.
6. Menyatakan ambiguitas, ketidakpastian, konflik, dan keterbatasan informasi.
7. Menghasilkan keluaran semantik terstruktur yang dapat divalidasi.
8. Mengukur akurasi, cakupan, grounding, serta biaya komputasi secara reproducible.

## 1.2 Secondary Objective

ADI sekaligus merupakan lingkungan pembelajaran dan eksperimen AI Engineering. Pengembangannya ditujukan untuk membangun kompetensi praktis dalam:

- Python dan pengolahan data.
- Fundamental Machine Learning dan evaluasi model.
- NLP dan representasi teks.
- Transformer, LLM, dan model multimodal.
- Data annotation, eksperimen, benchmarking, dan error analysis.
- Perancangan sistem inference yang dapat diuji dan dikembangkan.

Nilai sebuah pendekatan ditentukan oleh hasil pengujian terhadap masalah ADI, bukan popularitas framework atau modelnya.

---

# 2. Project Ownership and Boundaries

## 2.1 ADI Owns

ADI bertanggung jawab merancang serta mengembangkan:

- Dataset dan fixture eksperimen.
- Kontrak input internal untuk eksperimen dan inference.
- Validasi input dan normalisasi konteks yang diperlukan inference.
- Text, table, dan visual context construction.
- Model adapters dan eksperimen inference.
- Entity, event, rule, condition, dan relation extraction.
- Evidence attribution dan source-reference verification.
- Entity resolution dan semantic consolidation.
- Structured semantic output dan output validation.
- Evaluation datasets, benchmarks, error taxonomy, dan reports.

Kewenangan desain untuk komponen-komponen tersebut berasal dari kebutuhan ADI sendiri.

## 2.2 Project Boundaries

- ADI merupakan proyek terpisah yang dapat dikembangkan, diuji, dan dievaluasi tanpa menjalankan aplikasi lain.
- Input eksperimen harus dapat disediakan melalui fixture atau dataset lokal yang sah.
- Dokumen sumber diperlakukan sebagai **read-only evidence**; hasil interpretasi tidak boleh mengubah sumbernya.
- Identitas sumber dan lokasi bukti harus dapat dilacak secara konsisten di dalam eksperimen.
- Penyimpanan hasil eksperimen, jika diperlukan, menggunakan mekanisme milik ADI; tidak mengasumsikan akses database pihak lain.
- Kebutuhan pertukaran data dengan sistem eksternal, jika muncul di masa depan, harus didefinisikan sebagai kontrak dan adapter terpisah.
- Tidak ada kontrak, tipe, identifier, path, atau API dari repository lain yang menjadi dependency implisit proyek ini.

## 2.3 Non-Goals pada Tahap Awal

ADI tidak bertujuan untuk:

- Membangun platform pengelolaan dokumen end-to-end.
- Menciptakan PDF parser atau OCR engine dari nol tanpa kebutuhan eksperimen yang terukur.
- Melatih foundation model dari nol.
- Menjadikan autonomous agent sebagai prasyarat inference.
- Membangun production service, distributed orchestration, atau microservices sebelum dibutuhkan.
- Menetapkan model atau framework tertentu sebagai pilihan permanen tanpa evaluasi.
- Menganggap keluaran yang valid secara sintaksis otomatis benar secara semantik.
- Menganggap prediksi model sebagai fakta terverifikasi ketika bukti sumber tidak memadai.

Non-goals ini membatasi prioritas pengembangan awal, bukan melarang penelitian baru ketika ada justifikasi dan scope yang jelas.

---

# 3. Document Understanding Domain

## 3.1 Conceptual Input

ADI bekerja atas **document-derived evidence** yang merepresentasikan isi dokumen beserta rujukan ke sumbernya. Secara konseptual, eksperimen dapat menggunakan:

- **Document:** satu unit sumber dengan identifier yang konsisten di dalam dataset.
- **Sections / pages:** lokasi logis atau fisik informasi, bila tersedia.
- **Text spans:** teks yang dapat ditautkan ke konteks dan sumber.
- **Table elements:** konten tabel serta asosiasi baris dan kolom bila tersedia.
- **Visual elements:** gambar atau wilayah visual beserta akses ke kontennya jika sah.
- **Evidence locators:** penunjuk kembali ke bagian sumber yang mendukung pernyataan.

Ini adalah **model konseptual**, bukan schema final, nama format wajib, atau janji bahwa setiap dokumen memiliki semua elemen tersebut.

Format fixture, schema, batas validasi, serta aturan kompatibilitas harus dirumuskan dan diuji di milestone yang relevan. ADI tidak mengharuskan sebuah format upstream tertentu agar eksperimen fundamental dapat berjalan.

## 3.2 Input Limitations

ADI harus mampu menghadapi:

- Elemen dokumen yang hilang atau tidak lengkap.
- Reading order yang tidak pasti.
- Tabel dengan struktur ambigu.
- Visual yang tidak tersedia atau sulit dipahami.
- Referensi silang tanpa konteks yang cukup.
- Konten yang saling bertentangan.

Jika bukti tidak cukup untuk sebuah interpretasi, hasilnya harus menyatakan keterbatasan tersebut; tidak boleh menciptakan struktur atau relasi yang tidak didukung sumber.

## 3.3 Conceptual Output

Keluaran semantik ADI secara konseptual dapat memuat:

- Entities dan properties.
- Events, participants, dan time references.
- Relationships antar-entitas atau kejadian.
- Rules, conditions, exceptions, dan constraints.
- Polarity, modality, dan temporal qualifiers.
- Evidence references untuk setiap assertion.
- Ambiguity, conflict, atau unresolved interpretations.
- Metadata model dan konfigurasi eksperimen untuk reproduksibilitas.

Representasi tidak harus terbatas pada pola *subject–predicate–object*. Ontology dan output schema akan tumbuh berdasarkan kebutuhan eksperimen dan bukti evaluasi; belum diputuskan secara permanen di Project Context ini.

---

# 4. Target Capabilities

| Capability | Target behavior |
|---|---|
| Document classification | Mengenali karakteristik atau kategori dokumen. |
| Entity extraction | Mengidentifikasi entitas dan nilai penting. |
| Entity interpretation | Menentukan peran entitas di dalam konteks. |
| Event extraction | Mengidentifikasi aktivitas, pelaku, objek, dan waktu. |
| Relation extraction | Menemukan hubungan yang didukung informasi sumber. |
| Rule & condition extraction | Memahami kewajiban, larangan, prasyarat, pengecualian, dan kondisi. |
| Coreference resolution | Mengidentifikasi penyebutan yang mengacu ke entitas sama. |
| Cross-section interpretation | Menghubungkan informasi dari bagian dokumen yang berbeda. |
| Visual interpretation | Memahami informasi dari gambar, diagram, dan screenshot. |
| Evidence attribution | Mengaitkan setiap klaim dengan sumber yang dapat diverifikasi. |
| Semantic consolidation | Menggabungkan hasil, menghapus duplikasi secara terkendali, dan memeriksa konflik. |

Kemampuan di atas merupakan **target bertahap**, bukan klaim bahwa seluruh fungsi tersedia sejak milestone pertama.

---

# 5. Conceptual Inference Lifecycle

```text
Document Evidence / Research Fixture
                |
                v
         Input Validation
                |
                v
       Context Construction
                |
                v
      Model / Method Inference
                |
                v
      Semantic Interpretation
                |
                v
       Evidence Attribution
                |
                v
      Consolidation (if needed)
                |
                v
        Output Validation
                |
                v
    Structured Semantic Result
                |
                v
       Evaluation & Analysis
```

Diagram adalah alur tanggung jawab konseptual, **bukan** keputusan untuk membuat setiap tahapan sebagai service, package, proses, atau model terpisah. Pemecahan modul dan entrypoint ditentukan dalam Stack Context dan Implementation Context setelah kebutuhan konkret diketahui.

## 5.1 Model-Agnostic Strategy

Pendekatan yang dapat dibandingkan meliputi model ML sederhana, specialized NLP model, general-purpose language model, multimodal model, serta kombinasi metode. Keputusan eksperimen mempertimbangkan kualitas, data, latency, kebutuhan perangkat, dan kemudahan reproduksi.

## 5.2 Resource-Aware Development

Baseline pengembangan adalah eksperimen lokal yang dapat dijalankan dengan Python. CPU dipakai untuk eksperimen dasar; penggunaan GPU menjadi opsi jika dibenarkan oleh kebutuhan model dan hasil pengukuran. Cloud atau remote inference tidak menjadi asumsi wajib proyek.

---

# 6. Research and Evaluation Method

ADI memakai **evaluation-driven development**:

```text
Define Task & Hypothesis
          |
          v
Define Expected Outputs
          |
          v
Prepare Representative Data
          |
          v
Annotate / Establish Ground Truth
          |
          v
Implement Reproducible Baseline
          |
          v
Run Experiment & Collect Results
          |
          v
Evaluate and Analyze Errors
          |
          v
Decide: Improve / Compare / Reject
```

## 6.1 Dataset Principles

Dataset dikembangkan agar mewakili variasi dokumen dan jenis tantangan: teks padat, tabel, gambar, diagram, dokumen ambigu, serta referensi lintas bagian. Untuk setiap eksperimen yang relevan:

- Catat sumber, versi dataset, dan izin penggunaan data.
- Buat anotasi yang dapat ditelusuri ke elemen sumber.
- Tetapkan pedoman anotasi dan tinjau konsistensinya.
- Pisahkan data training, validation, dan test sesuai unit independensi yang benar.
- Cegah data leakage, terutama antarvariasi dari dokumen yang sama.
- Simpan prosedur pembentukan dataset dan seed bila berlaku.
- Jangan menyertakan dokumen rahasia atau yang tidak memiliki izin distribusi dalam repository publik.

## 6.2 Evaluation Dimensions

Evaluasi dipilih sesuai tugas, antara lain:

| Dimension | Example measurement |
|---|---|
| Entity extraction | Precision, recall, F1. |
| Relation extraction | Precision, recall, F1, relation direction. |
| Rule/condition interpretation | Correctness of conditions, exceptions, and negation. |
| Source grounding | Evidence accuracy dan invalid reference rate. |
| Factual support | Unsupported assertion rate. |
| Multimodal understanding | Visual task coverage dan grounded correctness. |
| Reliability | Schema validity, reproducibility, and stability. |
| Performance | Latency, throughput, memory, and compute usage. |

Sebuah model tidak dinyatakan berhasil hanya karena menghasilkan JSON yang valid. Ambang kelulusan akan ditetapkan berdasarkan baseline dan kebutuhan tugas yang benar-benar diuji.

## 6.3 Error Taxonomy

Minimal kategori analisis meliputi: missing evidence, context assembly error, missed entity, incorrect classification, wrong relation, missing condition, incorrect negation, visual misinterpretation, invalid reference, unsupported interpretation, dan incomplete coverage.

Perubahan metode harus didukung hipotesis yang jelas tentang kategori kesalahan yang ingin diperbaiki, lalu dibandingkan terhadap baseline yang sama.

---

# 7. Engineering Principles

1. **Independent ownership:** kebutuhan ADI ditetapkan dalam ADI, bukan diturunkan diam-diam dari proyek eksternal.
2. **Reproducibility:** data, konfigurasi, kode, environment, dan hasil benchmark dapat ditelusuri.
3. **Evidence before claims:** inference tidak boleh menjadi satu-satunya otoritas kebenaran.
4. **Explicit uncertainty:** hasil yang tidak pasti tetap dapat direpresentasikan tanpa dipaksakan menjadi fakta.
5. **Minimal necessary complexity:** mulai dari implementasi sederhana dan tambah abstraksi setelah ada kebutuhan.
6. **Replaceable models:** pilihan model tidak mengunci keseluruhan arsitektur.
7. **Testable contracts:** batas input/output yang digunakan komponen harus eksplisit dan diuji.
8. **Controlled evolution:** perubahan pipeline mempertahankan alasan, benchmark, dan dampaknya; jalur alternatif tidak diciptakan hanya untuk menghindari perbaikan pendekatan yang ada.
9. **Separation of concerns:** Project Context menyatakan *why/what*; Stack Context memilih *technology/instruments*; Implementation Context menetapkan *how/files/contracts/tests*.

---

# 8. Roadmap

## ADI-001 — Python & ML Experiment Foundation

**Objective:** Menyiapkan fondasi eksperimen Machine Learning yang bersih, teruji, dan reproducible.

**Deliverables:** struktur project Python, konfigurasi environment dan dependency, pengujian dasar, notebook eksperimen, dataset contoh yang sah, baseline text classification, serta laporan evaluasi pertama.

**Learning outcomes:** Python data handling, features/labels, training dan inference, dataset splitting, precision/recall/F1, overfitting, serta data leakage.

**Boundary:** Tidak membangun production inference service, model multimodal, fine-tuning, atau integrasi sistem eksternal.

## ADI-002 — Document Evidence Dataset & Ground Truth

**Objective:** Menetapkan format fixture dan dataset evaluasi semantik yang dimiliki ADI.

**Deliverables:** documented evidence fixture format, reader/validator, document samples, annotation guide, initial ontology candidates, gold dataset, dan document-level split.

## ADI-003 — Text Semantic Inference Baseline

**Objective:** Menguji ekstraksi entitas, peristiwa, relasi, aturan, dan kondisi berbasis teks.

**Deliverables:** context construction prototype, structured output candidate, baseline inference adapter, evidence checking, evaluation report, dan error analysis.

## ADI-004 — Model and Method Comparison

**Objective:** Membandingkan pendekatan inference secara objektif.

**Deliverables:** repeatable benchmark harness, comparable adapters, accuracy/resource/latency comparison, dan recommendation berdasarkan evidence.

## ADI-005 — Multimodal Document Understanding

**Objective:** Mengevaluasi interpretasi elemen visual dan hubungan text–table–image.

**Deliverables:** visual evidence fixtures, multimodal experiments, evidence attribution tests, cross-modal evaluation, dan analysis report.

## ADI-006 — Semantic Consolidation & Reliability

**Objective:** Mengonsolidasikan hasil inference tanpa menghilangkan keterlacakan sumber.

**Deliverables:** entity resolution, deduplication, contradiction handling, cross-section links, provenance verification, dan structured semantic results.

## ADI-007 — Standalone Inference Interface & Readiness

**Objective:** Mengemas kemampuan yang sudah terbukti sebagai antarmuka inference ADI yang terdokumentasi dan dapat diuji secara mandiri.

**Potential deliverables:** versioned request/response contracts owned by ADI, bounded execution, error handling, model/config version reporting, integration-style tests using local consumers, dan readiness evidence.

Antarmuka eksternal atau konektor spesifik ke sistem tertentu **bukan keputusan dalam Project Context** dan hanya menjadi scope tersendiri jika kelak dibutuhkan serta disetujui.

---

# 9. Success Criteria

Keberhasilan ADI dinilai dari kemampuan untuk:

1. Mengolah input evidence yang valid sesuai kontrak ADI pada milestone terkait.
2. Mengekstraksi dan menginterpretasikan informasi sesuai benchmark yang ditetapkan.
3. Menghasilkan keluaran semantik terstruktur dengan referensi bukti yang dapat diverifikasi.
4. Menangani bukti yang kurang, ambiguitas, dan konflik tanpa mengarang kepastian.
5. Melaporkan kualitas, coverage, serta unsupported inference secara kuantitatif.
6. Mengulang eksperimen dengan dataset dan konfigurasi yang diketahui.
7. Memilih pendekatan/model berdasarkan hasil pembandingan, bukan asumsi.
8. Menunjukkan performa yang layak terhadap beban eksperimen yang didefinisikan.
9. Beroperasi sebagai proyek mandiri tanpa ketergantungan pada runtime, database, atau kontrak implisit aplikasi lain.

Setiap milestone memiliki acceptance criteria sendiri; target akhir tidak otomatis menjadi requirement bagi milestone pertama.