# ATLAS DOCUMENT INTELLIGENCE
## Project Context — v1.0

**Status:** Initial Project Planning  
**Project:** Atlas Document Intelligence (ADI)  
**Repository:** `adityaa11/atlas-document-intelligence`  
**Development Branch:** `worker1`  
**Primary Language:** Python  
**Development Environment:** VS Code + Python Virtual Environment  
**Target Integration:** Atlas (`ces-platform`, branch `atlas-rebase`)

---

# 1. Project Vision

Atlas Document Intelligence adalah sistem AI independen yang dirancang untuk memahami dokumen terstruktur maupun tidak terstruktur, khususnya PDF yang mengandung:

- Teks dan paragraf.
- Heading dan hierarki dokumen.
- Tabel dan data terstruktur.
- Gambar dan screenshot.
- Diagram dan flowchart.
- Referensi silang antarbab atau halaman.

Sistem harus mampu melakukan **data interpretation, entity extraction, event extraction, dan semantic relationship extraction** terhadap informasi yang terdapat dalam dokumen.

Tujuan akhirnya bukan hanya menghasilkan ringkasan atau daftar entitas, tetapi membentuk representasi pengetahuan terstruktur yang dapat diproses lebih lanjut oleh Atlas.

Setiap interpretasi dan relasi yang dihasilkan harus mempertahankan hubungan dengan sumber informasi aslinya.

## 1.1 Primary Objective

Membangun sebuah **model-agnostic multimodal semantic inference engine** yang mampu:

1. Mengonsumsi NDV1 sebagai representasi struktural dokumen.
2. Mengakses visual assets yang tersedia melalui referensi sumber yang sah.
3. Membentuk konteks yang relevan dari elemen dokumen.
4. Mengidentifikasi dan menginterpretasikan informasi penting.
5. Mengekstraksi entitas, event, aturan, kondisi, dan hubungan semantik.
6. Menghubungkan informasi lintas paragraf, tabel, dan gambar ketika bukti memungkinkan.
7. Mempertahankan source provenance.
8. Menghasilkan structured semantic output.
9. Memvalidasi hasil dan mencatat ketidakpastian.
10. Mengukur kualitas inference secara objektif.

## 1.2 Secondary Objective

Repository ini juga berfungsi sebagai lingkungan pembelajaran dan penelitian AI Engineering.

Pengembangan harus memungkinkan eksperimen terhadap berbagai pendekatan Machine Learning, NLP, Transformer, LLM, dan Vision-Language Model tanpa mengharuskan perubahan terhadap aplikasi utama Atlas.

---

# 2. Project Boundaries

## 2.1 Atlas Responsibilities

Atlas bertanggung jawab atas:

- Document ingestion.
- PDF processing dan perception.
- Pembuatan NDV1.
- Document identity dan source integrity.
- Penyimpanan dokumen dan visual assets.
- Pengelolaan lifecycle pekerjaan.
- Penerimaan dan validasi final hasil semantic inference.
- Penyimpanan dan penggunaan structured knowledge.

## 2.2 Atlas Document Intelligence Responsibilities

ADI bertanggung jawab atas:

- NDV1 input validation.
- Context assembly.
- Model inference.
- Entity, event, dan relationship extraction.
- Multimodal interpretation.
- Evidence attribution.
- Entity resolution.
- Semantic output validation.
- Model experimentation.
- Evaluation dan benchmarking.

ADI tidak mempunyai otoritas untuk mengubah PDF sumber, NDV1, atau database utama Atlas.

## 2.3 Explicit Exclusions

Project tidak akan:

- Menghidupkan kembali BSS atau SEM yang sudah dipangkas dari `atlas-rebase`.
- Mengimpor implementasi legacy tanpa evaluasi dan persetujuan.
- Mengubah kontrak NDV1 untuk mempermudah output model.
- Membangun OCR atau PDF parser baru tanpa bukti kebutuhan.
- Melatih foundation model dari nol.
- Membuat autonomous agent sebagai prasyarat inference.
- Memilih framework berdasarkan popularitas tanpa benchmark.
- Menyimpan hasil inference sebagai kebenaran final tanpa validasi.
- Mengimplementasikan integrasi produksi sebelum kualitas inference terbukti.

---

# 3. Input Contract — NDV1

Input struktural utama adalah `NormalizedDocument v1` milik Atlas.

Kontrak saat perencanaan ini terdapat di:

`ces-platform/packages/atlas-contracts/src/perception.ts`

Struktur utamanya:

```text
NormalizedDocument
├── version
├── executionId
├── artifactId
├── sourceSha256
├── perception
├── provider
└── pages[]
    ├── number
    ├── width?
    ├── height?
    ├── textBlocks[]
    │   ├── id
    │   ├── text
    │   ├── kind?
    │   ├── boundingBox?
    │   └── confidence?
    ├── tables[]
    │   ├── id
    │   ├── content
    │   └── boundingBox?
    └── visualRegions[]
        ├── id
        ├── label?
        ├── boundingBox?
        └── assetRef?
```

NDV1 merupakan representasi sumber, bukan semantic knowledge.

Interpretasi harus dilakukan pada tahap terpisah.

## 3.1 Known Structural Limitations

ADI harus memperhitungkan bahwa:

- NDV1 belum menyediakan semantic relationships.
- Urutan baca terpadu antara teks, tabel, dan gambar tidak dijamin oleh kontrak.
- Bounding box bersifat opsional.
- Visual region belum tentu memiliki asset yang dapat diakses.
- Tabel dan teks dapat membutuhkan asosiasi kontekstual tambahan.
- Hubungan lintas halaman tidak direpresentasikan secara eksplisit.

ADI tidak boleh mengarang informasi struktural yang tidak tersedia.

Jika informasi tidak mencukupi, hasil harus menyatakan keterbatasan tersebut.

## 3.2 Visual Input

Visual inference membutuhkan akses terhadap konten gambar yang sebenarnya, bukan hanya metadata `visualRegions`.

Strategi akses visual akan dirancang sebagai kontrak terpisah yang mempertahankan:

- Document identity.
- Page number.
- Visual region identity.
- Source asset identity.
- Source integrity.
- Authorization boundary.

Input visual tidak boleh menggunakan arbitrary URL yang dikendalikan model.

---

# 4. Target Semantic Capabilities

Kemampuan inference akan dikembangkan secara bertahap.

| Capability | Description |
|---|---|
| Document Classification | Mengidentifikasi jenis atau karakteristik dokumen |
| Entity Extraction | Mengidentifikasi orang, organisasi, sistem, objek, konsep, dan nilai |
| Entity Interpretation | Memahami peran atau fungsi entitas dalam konteks |
| Event Extraction | Mengidentifikasi aktivitas, pelaku, objek, dan waktu |
| Relation Extraction | Menemukan hubungan antara entitas, event, atau konsep |
| Rule Extraction | Mengidentifikasi aturan, persyaratan, larangan, dan kewajiban |
| Conditional Interpretation | Mengidentifikasi kondisi yang memengaruhi suatu hubungan |
| Coreference Resolution | Menentukan apakah beberapa penyebutan merujuk objek yang sama |
| Cross-Section Reasoning | Menggabungkan bukti dari bagian dokumen berbeda |
| Visual Interpretation | Memahami informasi yang hanya tersedia dalam gambar |
| Evidence Attribution | Menghubungkan hasil dengan sumber yang mendukungnya |

## 4.1 Semantic Representation

Output tidak dibatasi pada bentuk subject–predicate–object.

Representasi perlu mampu menampung:

- Entities.
- Events.
- Relationships.
- Rules.
- Conditions.
- Constraints.
- Temporal qualifiers.
- Polarity dan modality.
- Evidence references.
- Uncertainty atau unresolved interpretation.

Ontology awal harus memiliki definisi yang jelas tetapi tetap dapat diperluas berdasarkan domain.

Targetnya adalah representasi generik dengan ekstensi domain bila diperlukan, bukan satu ontology universal yang dianggap mampu menjelaskan seluruh jenis dokumen sejak awal.

---

# 5. Proposed Inference Architecture

```text
                  ATLAS
                    |
                    v
             NormalizedDocument
                    |
                    v
           NDV1 Input Adapter
                    |
                    v
           Context Construction
                    |
          +---------+---------+
          |                   |
          v                   v
    Text / Table        Visual Context
      Context             Context
          |                   |
          v                   v
    Text Inference      Vision Inference
          |                   |
          +---------+---------+
                    |
                    v
          Semantic Extraction
                    |
                    v
          Evidence Attribution
                    |
                    v
       Entity / Relation Resolution
                    |
                    v
           Output Validation
                    |
                    v
       Structured Semantic Knowledge
                    |
                    v
                  ATLAS
```

Diagram ini merupakan target konseptual.

Tidak semua komponen harus menjadi service atau model terpisah. Penggabungan atau pemisahan komponen harus berdasarkan kebutuhan fungsional, hasil evaluasi, dan batas operasional.

## 5.1 Model-Agnostic Design

Inference engine harus memungkinkan pergantian model tanpa perubahan terhadap kontrak input dan output.

Kandidat eksperimen dapat mencakup:

- General-purpose text LLM.
- Specialized NER model.
- Relation extraction model.
- Vision-Language Model.
- Hybrid inference strategies.

Model tertentu tidak boleh ditetapkan sebagai dependency arsitektur permanen sebelum evaluasi.

## 5.2 Model Execution

Project akan memprioritaskan local inference.

CPU merupakan execution baseline.

GPU dapat digunakan sebagai akselerator apabila benchmark menunjukkan manfaat yang diperlukan.

Remote inference provider dapat digunakan sebagai pembanding dengan otorisasi yang sesuai, tetapi tidak menjadi dependency wajib.

---

# 6. AI Engineering Methodology

Project menggunakan pendekatan **evaluation-driven development**.

Urutan standar:

```text
Define Task
    ↓
Define Expected Output
    ↓
Collect Representative Data
    ↓
Create Ground Truth
    ↓
Establish Baseline
    ↓
Run Inference
    ↓
Evaluate Predictions
    ↓
Analyze Errors
    ↓
Improve Model / Context / Method
    ↓
Repeat Evaluation
```

## 6.1 Dataset Development

Dataset akan terdiri dari dokumen dengan variasi:

- Digital PDF.
- Scanned PDF.
- Text-heavy documents.
- Table-heavy documents.
- Documents containing screenshots.
- Documents containing workflow diagrams.
- Documents containing cross-references.
- Documents containing conditional or ambiguous statements.

Setiap anotasi harus dapat ditelusuri ke dokumen sumber.

Dataset development harus memperhatikan:

- Data licensing dan kerahasiaan.
- Annotation consistency.
- Train/validation/test separation.
- Document-level data leakage.
- Dataset versioning.
- Reproducibility.

## 6.2 Evaluation Metrics

Minimal evaluasi:

| Metric | Purpose |
|---|---|
| Entity Precision | Mengukur ketepatan entitas yang ditemukan |
| Entity Recall | Mengukur cakupan entitas |
| Entity F1 | Menggabungkan precision dan recall |
| Relation Precision | Mengukur ketepatan relasi |
| Relation Recall | Mengukur cakupan relasi |
| Relation F1 | Evaluasi ekstraksi hubungan |
| Evidence Accuracy | Mengukur ketepatan bukti |
| Condition Accuracy | Memeriksa interpretasi persyaratan dan kondisi |
| Visual Coverage | Mengukur informasi visual yang berhasil diproses |
| Unsupported Claim Rate | Mengukur hubungan yang tidak didukung sumber |
| Latency | Mengukur waktu inference |
| Resource Usage | Mengukur konsumsi RAM, VRAM, dan CPU |
| Throughput | Mengukur kapasitas pemrosesan |

Tidak ada model yang dinyatakan production-ready hanya berdasarkan keberhasilan menghasilkan JSON atau lolos schema validation.

Target angka penerimaan harus ditentukan setelah evaluasi baseline dan sesuai kebutuhan penggunaan.

## 6.3 Error Analysis

Setiap kegagalan harus dapat diklasifikasikan, misalnya:

- Source information missing.
- Incorrect context construction.
- Entity not detected.
- Entity incorrectly classified.
- Incorrect relation direction.
- Missing condition.
- Incorrect negation.
- Incorrect visual interpretation.
- Invalid evidence reference.
- Unsupported inference.
- Incomplete source coverage.

Rekomendasi perbaikan harus mengacu pada kategori kegagalan, bukan asumsi bahwa model yang lebih besar selalu diperlukan.

---

# 7. Historical Experiment Awareness

Atlas sebelumnya telah melakukan eksperimen semantic extraction menggunakan Docling Graph dengan beberapa model lokal.

Eksperimen tersebut belum menghasilkan kualitas semantic extraction dan source attribution yang dapat diterima oleh Atlas.

Karena itu:

1. Hasil eksperimen terdahulu harus diperlakukan sebagai historical evidence.
2. Pendekatan yang sama tidak boleh diulang tanpa hipotesis perbaikan yang jelas.
3. Keberhasilan model menghasilkan output tidak sama dengan keberhasilan interpretasi.
4. Source attribution menjadi bagian utama evaluasi.
5. Perbandingan model harus menggunakan dataset dan kriteria yang konsisten.

ADI tidak mengasumsikan bahwa Docling Graph, GLiNER, GLiREL, atau LLM tertentu merupakan solusi final.

---

# 8. Technology Strategy

## 8.1 Foundation

| Component | Technology |
|---|---|
| Programming | Python |
| IDE | VS Code |
| Environment | Python venv |
| Package Management | pip pada tahap awal |
| Data Processing | NumPy, Pandas |
| Classical ML | Scikit-learn |
| Notebooks | Jupyter |
| Data Validation | Pydantic |
| Visualization | Matplotlib |
| Testing | pytest |
| Version Control | Git / GitHub |

## 8.2 Research Candidates

Instrumen berikut merupakan kandidat, bukan mandatory dependencies:

| Component | Candidate |
|---|---|
| Neural Models | PyTorch / Transformers |
| Entity Extraction | GLiNER |
| Relation Extraction | GLiREL |
| Multimodal Models | Open-weight VLM |
| Graph Processing | NetworkX |
| Annotation | Label Studio |
| Experiment Tracking | MLflow |
| Local Model Runtime | ONNX Runtime / Ollama / llama.cpp |
| HTTP Serving | FastAPI |

Dependency ditambahkan ketika ada eksperimen atau kebutuhan implementasi yang membenarkan penggunaannya.

Tidak semua library perlu diinstal pada project initialization.

---

# 9. Development Environment

Baseline pengembangan:

- Windows.
- VS Code.
- Python 3.
- Project-local `.venv`.
- CPU-first execution.

Hardware awal:

- Intel Core 7 240H.
- RAM 32 GB.
- NVIDIA RTX 4050 Laptop GPU.
- Dedicated VRAM 6 GB.

Perangkat tersebut menjadi baseline benchmark lokal.

GPU acceleration bukan persyaratan untuk menyelesaikan fundamental Machine Learning atau membangun evaluation infrastructure.

Project tidak boleh mengasumsikan semua model dapat berjalan dengan performa memadai pada CPU. Kelayakan setiap model harus dibuktikan melalui pengukuran nyata.

---

# 10. Development Roadmap

## ADI-001 — Python & ML Experiment Foundation

**Objective:** Membangun fondasi proyek Python yang bersih, dapat diuji, dan reproducible.

Deliverables:

- Project package structure.
- Python environment configuration.
- Dependency declaration.
- Unit test infrastructure.
- Jupyter Notebook environment.
- Sample dataset.
- Baseline text classifier.
- Initial evaluation report.

Target pembelajaran:

- Python data handling.
- Features and labels.
- Training and inference.
- Classification.
- Precision, recall, dan F1.
- Dataset splitting.
- Overfitting dan data leakage.

Scope ini tidak memerlukan integrasi Atlas atau model generatif.

## ADI-002 — NDV1 Dataset & Ground Truth

**Objective:** Menghasilkan dataset evaluasi yang merepresentasikan kebutuhan semantik Atlas.

Deliverables:

- NDV1 input reader.
- Representative document fixtures.
- Annotation format.
- Initial semantic ontology.
- Gold dataset.
- Dataset validation.
- Document-level dataset split.

Dataset tidak boleh memasukkan dokumen sensitif ke repository publik.

## ADI-003 — Text Semantic Inference Baseline

**Objective:** Menguji kemampuan model dalam mengekstraksi interpretasi dan relasi dari teks.

Deliverables:

- Context assembly prototype.
- Structured semantic output schema.
- Baseline inference adapter.
- Evidence references.
- Evaluation results.
- Error analysis report.

## ADI-004 — Specialized Model Comparison

**Objective:** Membandingkan general-purpose LLM dengan specialized NLP models.

Deliverables:

- Model benchmarking harness.
- Model-specific adapters.
- Comparable evaluation dataset.
- Accuracy report.
- Latency and resource report.
- Model selection recommendation.

## ADI-005 — Multimodal Inference

**Objective:** Memahami informasi yang berada dalam gambar, tabel kompleks, screenshot, dan flowchart.

Deliverables:

- Visual context adapter.
- Multimodal inference experiments.
- Visual evidence attribution.
- Cross-modal relationship extraction.
- Multimodal evaluation report.

## ADI-006 — Semantic Consolidation

**Objective:** Menggabungkan hasil inference menjadi representasi pengetahuan yang konsisten.

Deliverables:

- Entity resolution.
- Relation consolidation.
- Conflict detection.
- Cross-section relationship handling.
- Evidence verification.
- Structured semantic knowledge output.

## ADI-007 — Atlas Integration

**Objective:** Mengintegrasikan inference engine yang sudah tervalidasi dengan Atlas.

Proposed integration:

- Internal HTTP API.
- Versioned inference request/response.
- Authenticated communication.
- Bounded request processing.
- Async job support bila diperlukan.
- Model and configuration versioning.
- Integration tests.

Scope ini hanya dilakukan setelah evaluation evidence memadai.

---

# 11. Project Engineering Principles

## 11.1 Reproducibility

Eksperimen harus mencatat:

- Dataset version.
- Model identity dan version.
- Model configuration.
- Prompt version, jika menggunakan LLM.
- Preprocessing configuration.
- Evaluation configuration.
- Runtime environment.
- Inference result.
- Evaluation metrics.

## 11.2 Source Grounding

Model tidak boleh menjadi otoritas tunggal atas identitas sumber.

Evidence references harus diverifikasi terhadap input yang tersedia.

Jika suatu source reference tidak valid, hasil harus ditolak atau ditandai sebagai unsupported.

## 11.3 Uncertainty Handling

Sistem harus mampu membedakan:

- Supported interpretation.
- Ambiguous interpretation.
- Insufficient evidence.
- Unresolved reference.
- Unsupported interpretation.

Tidak semua data harus dipaksakan menjadi semantic relationship.

## 11.4 Controlled Complexity

- Mulai dari implementasi sederhana.
- Hindari premature microservices.
- Hindari agent orchestration tanpa kebutuhan.
- Hindari GPU dependency sebelum diperlukan.
- Hindari premature fine-tuning.
- Hindari ontology yang terlalu kompleks sebelum ada evidence.
- Jangan mencampur eksperimen riset dengan production integration.

## 11.5 Independent Repository

ADI merupakan project independen dari `ces-platform`.

Integrasi lintas repository menggunakan kontrak eksplisit, bukan akses langsung terhadap internal database atau implementasi Atlas.

---

# 12. Definition of Project Success

Project dinyatakan berhasil secara teknis jika ADI mampu:

1. Memproses input NDV1 yang valid.
2. Menginterpretasikan informasi teks dan visual sesuai cakupan yang didefinisikan.
3. Mengekstraksi entitas, event, kondisi, dan hubungan semantik.
4. Menghasilkan output yang mengikuti schema.
5. Mempertahankan bukti sumber yang dapat diverifikasi.
6. Menyatakan ketidakpastian dan keterbatasan.
7. Mencapai target kualitas yang ditetapkan melalui benchmark.
8. Menunjukkan kapasitas inference yang layak untuk beban kerja sasaran.
9. Menghasilkan output secara reproducible pada konfigurasi yang terkendali.
10. Berkomunikasi dengan Atlas tanpa mengubah kontrak perception yang sudah ada.

Keberhasilan setiap milestone harus dibuktikan secara terpisah.

---

# 13. Immediate Next Action

**Next Scope: ADI-001 — Python & ML Experiment Foundation**

Current environment:

- Repository `atlas-document-intelligence` sudah dibuat.
- Development branch `worker1` sudah tersedia.
- VS Code sudah diinstal.
- Python virtual environment `.venv` sudah dibuat dan berhasil diaktifkan.

Langkah berikutnya adalah menyusun implementation context khusus ADI-001.

ADI-001 harus tetap kecil dan berfokus pada fondasi Machine Learning.

Belum diperlukan:

- Production inference service.
- Atlas HTTP integration.
- LLM deployment.
- VLM deployment.
- Fine-tuning.
- Autonomous agents.
- Production semantic extraction pipeline.

**Guiding Principle:**

> Build measurable understanding before building production intelligence.

Atlas Document Intelligence harus berkembang berdasarkan hasil eksperimen dan evaluasi, bukan asumsi tentang kemampuan model atau framework.