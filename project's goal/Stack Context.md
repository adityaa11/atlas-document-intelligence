# ATLAS DOCUMENT INTELLIGENCE
## Technology Stack & Engineering Instruments Context — v1.0

**Document Type:** Project Stack Context  
**Status:** Proposed Baseline — for review before implementation  
**Project:** Atlas Document Intelligence (ADI)  
**Repository:** `adityaa11/atlas-document-intelligence`  
**Development Branch:** `worker1`  
**Integration Target:** `ces-platform`, branch `atlas-rebase`  
**Primary Runtime:** Python  
**Execution Strategy:** Local, CPU-first, model-agnostic

---

# 1. Purpose

Dokumen ini menetapkan technology stack, engineering instruments, dependency boundaries, dan strategi pemilihan model untuk pengembangan Atlas Document Intelligence.

Dokumen ini menjadi referensi bagi seluruh Implementation Context ADI.

Implementation tidak boleh memperkenalkan framework, model runtime, database, atau dependency arsitektural baru hanya karena dianggap praktis atau populer.

Setiap perubahan stack harus mempunyai:

- Kebutuhan teknis yang jelas.
- Alasan pemilihan.
- Alternatif yang dipertimbangkan.
- Dampak dependency.
- Bukti kompatibilitas.
- Evaluation criteria.
- Persetujuan sebelum dianggap sebagai baseline baru.

## 1.1 Engineering Objective

Membangun multimodal semantic inference engine yang menerima NDV1 dan sumber visual terkait untuk menghasilkan:

- Data interpretations.
- Entities.
- Events.
- Semantic relationships.
- Rules and constraints.
- Conditions and temporal information.
- Evidence-backed structured knowledge.

Sistem harus dapat menggunakan model dan strategi inference berbeda tanpa mengubah kontrak input struktural Atlas.

## 1.2 Stack Selection Principles

Technology stack dipilih berdasarkan:

1. Reproducibility.
2. CPU-first feasibility.
3. Model independence.
4. Structured input and output.
5. Source provenance.
6. Evaluation support.
7. Dependency isolation.
8. Maintainability.
9. Compatibility with Atlas.
10. Evidence-based model selection.

Model accuracy lebih penting daripada sekadar kemudahan menghasilkan output.

---

# 2. Stack Classification

Setiap teknologi memiliki satu dari empat status.

| Status | Meaning |
|---|---|
| SELECTED | Dipilih sebagai bagian dari arsitektur atau workflow proyek |
| EXPERIMENTAL | Kandidat yang hanya digunakan untuk eksperimen terukur |
| DEFERRED | Dipertimbangkan, tetapi belum diperlukan |
| EXCLUDED | Tidak boleh dimasukkan dalam baseline saat ini |

Status `SELECTED` tidak otomatis berarti dependency harus diinstal pada ADI-001.

Dependency baru diaktifkan ketika scope implementasinya membutuhkan teknologi tersebut.

---

# 3. Programming & Project Foundation

## 3.1 Programming Language

**Selected: CPython 3.12**

Python dipilih karena menyediakan ekosistem utama untuk:

- Machine Learning.
- Natural Language Processing.
- Deep Learning.
- Transformer inference.
- Computer vision.
- Data annotation and evaluation.
- Model serving.

Python 3.12 dipilih sebagai baseline kompatibilitas yang konservatif.

Target repository:

```text
Python: 3.12.x
Implementation: CPython
Operating System: Windows development
Execution: CPU-first
```

Penggunaan Python 3.13 atau lebih baru membutuhkan validasi kompatibilitas dependency dan test suite. Tidak perlu membuat ulang environment yang sudah bekerja sebelum ada alasan teknis.

## 3.2 Development Environment

| Instrument | Status | Function |
|---|---|---|
| VS Code | SELECTED | Primary IDE |
| Python Extension | SELECTED | Python debugging dan interpreter management |
| Pylance | SELECTED | Type analysis dan language support |
| Jupyter Extension | SELECTED | Notebook execution |
| Python venv | SELECTED | Environment isolation |
| Git | SELECTED | Version control |
| GitHub | SELECTED | Source repository dan collaboration |

Virtual environment menggunakan direktori `.venv` dan tidak disimpan ke Git.

## 3.3 Dependency Management

**Selected: uv**

Primary instruments:

```text
pyproject.toml
uv.lock
.python-version
.venv/
```

Responsibilities:

- `pyproject.toml`: project metadata, dependency declarations, tool configuration.
- `uv.lock`: resolved dependency versions.
- `.python-version`: preferred Python version.
- `.venv`: local environment.

Dependency dikelompokkan berdasarkan kebutuhan, misalnya:

```text
Core
  Pydantic

Development
  pytest
  Ruff

ML Experiments
  NumPy
  Pandas
  Scikit-learn
  Matplotlib
  Jupyter / ipykernel

Neural Experiments
  PyTorch
  Transformers

Serving
  FastAPI
  Uvicorn
```

Pengelompokan implementasi dapat menggunakan dependency groups atau optional dependencies sesuai kebutuhan packaging.

Aturan:

- Jangan menggunakan `pip install` acak sebagai sumber konfigurasi permanen.
- Jangan commit `.venv`.
- Jangan commit cache model.
- Jangan commit model weights besar secara langsung.
- Jangan mengedit `uv.lock` secara manual.
- Existing virtual environment harus diperiksa sebelum disinkronkan; jangan dihapus tanpa kebutuhan.

Exact resolved package versions ditentukan ketika dependency pertama kali diaktifkan dan dicatat dalam lockfile.

---

# 4. Data Engineering Stack

## 4.1 Selected Instruments

| Instrument | Status | Purpose |
|---|---|---|
| Python standard library | SELECTED | JSON, hashing, filesystem, logging |
| NumPy | SELECTED | Numerical operations untuk eksperimen |
| Pandas | SELECTED | Dataset processing dan analysis |
| JSON / JSONL | SELECTED | Input fixtures, annotations, predictions |
| Pydantic v2 | SELECTED | Typed schemas dan data validation |

## 4.2 Data Format Decisions

Primary exchange format:

**JSON**

Dataset dan anotasi eksperimen:

**JSONL**

Tabular analysis:

**Pandas DataFrame**

CSV boleh digunakan sebagai export sederhana, tetapi tidak menjadi format utama untuk relasi kompleks yang membutuhkan nested evidence.

Parquet, Arrow, dan database analytics ditunda sampai terdapat kebutuhan volume atau performa yang terukur.

## 4.3 Validation

Pydantic v2 digunakan untuk memvalidasi:

- NDV1-derived inputs.
- Experiment configuration.
- Annotation records.
- Model prediction records.
- Evaluation records.
- Semantic output candidates.

Input validation harus ketat.

Unknown version, invalid field type, dan malformed source references tidak boleh dinormalisasi secara diam-diam.

Kontrak TypeScript NDV1 di Atlas tetap menjadi sumber otoritatif bagi format input. Model Python harus diuji menggunakan fixture yang sesuai dengan kontrak tersebut.

---

# 5. Atlas Input & Document Representation

## 5.1 Primary Input

**NormalizedDocument v1 (NDV1)**

Authoritative source:

```text
ces-platform/
  packages/
    atlas-contracts/
      src/perception.ts
```

ADI tidak bertugas mengubah raw PDF menjadi NDV1.

Document perception telah berada dalam tanggung jawab Atlas.

## 5.2 Input Adapter

**Selected: Custom NDV1 Adapter**

Implementation language: Python.

Responsibilities:

- Parse and validate NDV1.
- Preserve document identity.
- Preserve page identities.
- Preserve text block IDs.
- Preserve table IDs.
- Preserve visual region IDs.
- Preserve available geometry.
- Preserve source SHA-256.
- Expose document elements to inference modules.

NDV1 adapter tidak diperbolehkan membuat semantic interpretations.

## 5.3 Document Context Assembly

**Selected: Custom Structure-Aware Context Assembler**

Tidak menggunakan generic character-based chunking sebagai strategi utama.

Assembler harus mempertimbangkan:

- Page boundaries.
- Available heading structure.
- Text blocks.
- Tables.
- Visual regions.
- Available source coordinates.
- Context dependency.
- References between sections.

NDV1 tidak menjamin reading order terpadu antartipe elemen.

Jika urutan atau asosiasi elemen tidak dapat ditentukan secara tepercaya, assembler harus mempertahankan uncertainty alih-alih menciptakan hubungan struktural palsu.

Context construction strategy harus dapat diuji dan dibandingkan.

---

# 6. Machine Learning Foundation

## 6.1 Classical ML

**Selected: Scikit-learn**

Initial learning and experimentation tasks:

- Text classification.
- Feature extraction.
- Supervised learning.
- Model fitting.
- Prediction.
- Train/validation/test splitting.
- Precision, recall, and F1 evaluation.
- Confusion matrix.
- Error analysis.

Initial baseline:

```text
Text
  ↓
TF-IDF Vectorizer
  ↓
Logistic Regression Classifier
  ↓
Predicted Label
  ↓
Evaluation
```

Model ini hanya merupakan baseline pembelajaran dan eksperimen.

Tidak ditetapkan sebagai model final untuk semantic relationship extraction.

## 6.2 Supporting Instruments

| Instrument | Role |
|---|---|
| Scikit-learn | Training dan evaluation |
| NumPy | Numerical representation |
| Pandas | Dataset preparation |
| Matplotlib | Evaluation visualization |
| Jupyter | Interactive experiments |

ADI-001 tidak membutuhkan PyTorch, GPU, atau external LLM.

---

# 7. Neural NLP & Deep Learning Stack

## 7.1 Framework

**Selected for future neural experiments: PyTorch**

PyTorch digunakan ketika proyek membutuhkan:

- Neural model inference.
- Transformer execution.
- Fine-tuning.
- Gradient-based optimization.
- GPU acceleration experiments.

Tidak menjadi mandatory dependency pada ADI-001.

## 7.2 Model Ecosystem

**Selected for future experiments: Hugging Face Transformers**

Responsibilities:

- Model loading.
- Tokenizer loading.
- Model configuration.
- Pretrained checkpoint execution.
- Transformer inference.
- Fine-tuning support when justified.

Model weights harus disertai identitas model, revision, license, dan konfigurasi yang dapat dilacak.

## 7.3 Specialized NLP Candidates

| Candidate | Status | Evaluation Purpose |
|---|---|---|
| GLiNER | EXPERIMENTAL | Named entity extraction |
| GLiREL | EXPERIMENTAL | Relationship extraction |
| Joint entity-relation models | EXPERIMENTAL | Menguji pendekatan ekstraksi terpadu |
| General-purpose text LLM | EXPERIMENTAL | Contextual semantic extraction |

Tidak ada kandidat yang secara otomatis dipilih menjadi production model.

Previous Docling Graph experiments menjadi historical failure evidence dan tidak diulang tanpa hipotesis perubahan yang terukur.

---

# 8. Multimodal Inference Stack

## 8.1 Vision-Language Models

**Status: EXPERIMENTAL**

Vision-Language Model diperlukan apabila informasi semantik berasal dari:

- Screenshots.
- Flowcharts.
- Charts.
- Diagrams.
- Figures.
- Mixed text-image regions.

Tidak boleh menganggap OCR saja cukup untuk memahami hubungan antarobjek visual.

## 8.2 Model Selection

Belum ada satu VLM yang dipilih sebagai baseline permanen.

Setiap kandidat akan dibandingkan berdasarkan:

- Diagram interpretation accuracy.
- Visual entity extraction.
- Relationship direction accuracy.
- Text-to-visual evidence consistency.
- Source grounding.
- CPU/GPU memory usage.
- Inference latency.
- Licensing and deployment restrictions.

Pengujian dilakukan setelah visual inputs dan evaluation dataset siap.

## 8.3 Visual Source Access

ADI menerima visual assets melalui input yang eksplisit dan terikat ke identitas sumber.

Visual input harus mempunyai:

```text
Document identity
Page identity
Visual region identity
Asset identity
Content integrity
Authorized access
```

Model tidak boleh menentukan sendiri URL sumber atau membuat referensi gambar palsu.

Jika visual asset tidak tersedia, inference harus mencatat bahwa visual coverage tidak lengkap.

## 8.4 PDF Processing Instruments

Docling, OCR engines, dan PDF rendering libraries tidak menjadi primary document-ingestion dependency di ADI.

Tools tersebut hanya boleh digunakan untuk:

- Benchmark preparation.
- Evaluation fixture inspection.
- Investigasi kesalahan perception.
- Eksperimen yang disetujui secara eksplisit.

Primary production input tetap NDV1 dan visual assets yang disediakan Atlas.

---

# 9. Inference Engine Architecture

## 9.1 Selected Architectural Style

**Modular Python package, model-agnostic execution.**

Initial implementation adalah Python library dan CLI.

Tidak langsung memerlukan HTTP server, message broker, atau autonomous agent.

Conceptual architecture:

```text
NDV1
  |
  v
Input Validation
  |
  v
Context Assembly
  |
  +-------------------------+
  |                         |
  v                         v
Text Context          Visual Context
  |                         |
  v                         v
Text Inference        Visual Inference
  |                         |
  +------------+------------+
               |
               v
       Semantic Candidates
               |
               v
       Evidence Verification
               |
               v
       Semantic Consolidation
               |
               v
       Validated Output
```

Komponen tidak harus menjadi class atau service terpisah jika belum diperlukan.

## 9.2 Model Adapter Boundary

Setiap model adapter secara konseptual menerima:

```text
Inference input
Model configuration
Execution context
```

Dan menghasilkan:

```text
Predictions
Model metadata
Execution metadata
```

Adapter tidak mempunyai otoritas untuk:

- Mengubah NDV1.
- Memodifikasi original PDF.
- Menulis langsung ke database Atlas.
- Menentukan final truth state.
- Mengarang source identities.

Nama dan struktur interface final akan ditentukan ketika kebutuhan pertama terbukti melalui eksperimen.

## 9.3 Local Inference Runtime

| Runtime | Status | Intended Use |
|---|---|---|
| Scikit-learn runtime | SELECTED | Classical ML |
| PyTorch CPU | DEFERRED | Neural inference |
| ONNX Runtime CPU | EXPERIMENTAL | CPU inference optimization |
| llama.cpp | EXPERIMENTAL | Quantized local generative LLM |
| Ollama | EXPERIMENTAL | Local model experimentation |
| vLLM | DEFERRED | Higher-throughput LLM serving |

Kita tidak membutuhkan semua runtime tersebut.

Satu model boleh memiliki beberapa backend yang diuji, tetapi hasil evaluasi harus mencatat backend dan konfigurasi yang digunakan.

---

# 10. Knowledge Representation Stack

## 10.1 Structured Output

**Selected: Pydantic-based semantic result schema**

Output harus dapat mewakili:

- Entities.
- Events.
- Relationships.
- Conditions.
- Constraints.
- Evidence.
- Unresolved interpretations.

Schema semantik final belum dibekukan dalam dokumen Stack Context ini.

Ontology dan semantics akan didefinisikan melalui ADI-002 berdasarkan contoh dokumen yang representatif.

## 10.2 Graph Representation

**Selected when graph operations are required: NetworkX**

NetworkX digunakan untuk eksperimen:

- Entity relationship traversal.
- Connected components.
- Duplicate relationship inspection.
- Graph consistency checks.
- Relationship consolidation.

Graph tidak menjadi satu-satunya representasi semantik.

Tidak semua makna dapat direpresentasikan dengan benar hanya melalui hubungan subject–predicate–object.

## 10.3 Graph Database

**Status: DEFERRED**

Neo4j, graph databases, dan distributed graph infrastructure belum diperlukan.

ADI awalnya menghasilkan portable structured JSON.

Pemilihan database graph baru dilakukan setelah kebutuhan persistent graph queries, volume, dan performance terbukti.

---

# 11. Evaluation & Experimentation Instruments

## 11.1 Evaluation Stack

| Instrument | Status | Purpose |
|---|---|---|
| Scikit-learn metrics | SELECTED | Classification metrics |
| Custom evaluation scripts | SELECTED | Semantic evaluation |
| pytest | SELECTED | Automated checks |
| Matplotlib | SELECTED | Charts and error visualization |
| JSON evaluation reports | SELECTED | Machine-readable results |
| MLflow | DEFERRED | Experiment tracking |
| Label Studio | DEFERRED | Human annotation workflow |

## 11.2 Evaluation Requirements

Evaluation harus membedakan:

**Prediction correctness**

Apakah entitas dan relasi yang ditemukan benar?

**Prediction completeness**

Apakah informasi yang relevan berhasil ditemukan?

**Grounding correctness**

Apakah evidence benar-benar mendukung interpretasi?

**Context correctness**

Apakah negasi, kondisi, dan status temporal dipahami?

**Visual correctness**

Apakah informasi dari gambar diinterpretasikan dengan benar?

**Execution performance**

Berapa latency, memory usage, dan throughput?

## 11.3 Experiment Record

Setiap eksperimen model harus mencatat:

```text
experiment_id
dataset_version
model_id
model_revision
runtime
device
configuration
prompt_version (if applicable)
random_seed (if applicable)
git_commit
metrics
latency
memory_usage
output_artifacts
```

Eksperimen LLM nondeterministik tidak boleh diklaim bit-for-bit reproducible hanya karena configuration dan seed telah dicatat.

## 11.4 Experiment Tracking

Initial tracking menggunakan JSON reports dan version-controlled experiment configuration.

MLflow diaktifkan setelah eksperimen cukup banyak sehingga manual comparison tidak lagi memadai.

Tidak diperlukan MLflow server pada ADI-001.

---

# 12. Testing, Quality & CI

## 12.1 Testing Framework

**Selected: pytest**

Test categories:

- Unit tests.
- Schema validation tests.
- Dataset validation tests.
- Evaluation metric tests.
- Deterministic inference tests.
- Source grounding tests.
- Integration tests.
- Model benchmark tests.

Model benchmark tests dipisahkan dari unit tests biasa karena dapat mengunduh model, mengonsumsi memori besar, dan membutuhkan waktu lebih lama.

## 12.2 Formatting & Linting

**Selected: Ruff**

Responsibilities:

- Python code linting.
- Python code formatting.
- Consistent project style.
- Static quality checks.

Konfigurasi berada di `pyproject.toml`.

## 12.3 Type Checking

Python type annotations diwajibkan untuk public interfaces yang dikembangkan.

Pylance digunakan dalam development.

Dedicated command-line type checker dapat ditambahkan ketika public API dan schema complexity membenarkannya.

## 12.4 Continuous Integration

**Selected: GitHub Actions**

Initial CI harus:

- Menyiapkan Python baseline.
- Menginstal dependencies sesuai lockfile.
- Menjalankan Ruff lint.
- Menjalankan Ruff format check.
- Menjalankan pytest.
- Memastikan test tidak membutuhkan GPU.
- Memastikan test tidak membutuhkan remote inference provider.

CI tidak boleh otomatis menjalankan model besar atau memerlukan production credentials.

---

# 13. Hardware & Execution Policy

## 13.1 Development Hardware

Current local baseline:

```text
CPU: Intel Core 7 240H
RAM: 32 GB
GPU: NVIDIA RTX 4050 Laptop
VRAM: 6 GB
OS: Windows
```

## 13.2 CPU-First Requirement

Setiap scope awal harus dapat berjalan menggunakan CPU.

GPU tidak boleh menjadi syarat untuk:

- Dataset preparation.
- Annotation validation.
- Classical ML training.
- Model evaluation framework.
- NDV1 adapters.
- Semantic output validation.
- Unit testing.

Neural model experiments dapat menggunakan GPU jika diperlukan, tetapi GPU dependency harus dinyatakan secara eksplisit.

## 13.3 Resource Measurements

Untuk neural inference, setiap benchmark harus mencatat:

- Model load time.
- Input size.
- Inference time.
- Peak RAM.
- Peak VRAM, jika menggunakan GPU.
- Per-document throughput.
- Failure atau out-of-memory events.

Tidak ada asumsi bahwa self-hosted inference memiliki kapasitas tanpa batas.

---

# 14. Serving & Atlas Integration Stack

## 14.1 Serving Framework

**Selected for future integration: FastAPI**

ASGI server:

**Uvicorn**

Status implementasi: DEFERRED.

FastAPI akan digunakan jika inference library sudah mempunyai input/output contract dan evaluation evidence yang dapat diterima.

## 14.2 Communication

Proposed transport:

```text
Atlas
  |
  | Authenticated Internal HTTP
  v
Atlas Document Intelligence
  |
  | Inference Result
  v
Atlas
```

Transport menggunakan versioned JSON contracts.

FastAPI tidak menjadi pemilik original documents, NDV1, atau knowledge persistence milik Atlas.

## 14.3 Job Processing

Synchronous HTTP boleh digunakan untuk small bounded inference.

Long-running inference akan membutuhkan asynchronous processing, timeouts, retry discipline, dan idempotency.

Exact job orchestration strategy belum ditetapkan.

Tidak akan menambahkan Celery, Redis, RabbitMQ, atau broker lain sebelum kebutuhan tersebut dibuktikan.

---

# 15. Source & Data Security

Repository bersifat public.

Karena itu, hal berikut tidak boleh di-commit tanpa izin dan review:

- Confidential PDFs.
- Customer documents.
- Documents containing personal data.
- API credentials.
- Local environment secrets.
- Model caches.
- Raw inference logs containing sensitive content.
- Large model checkpoints.

`.gitignore` harus mencakup local environments, caches, generated artifacts, dan sensitive local datasets.

Public datasets harus memiliki sumber, hak penggunaan, dan license yang jelas.

Model open-weight juga harus diperiksa syarat penggunaan dan distribusinya sebelum dimasukkan ke workflow.

Local inference tidak otomatis membuat seluruh pipeline aman; file handling, access control, logging, dan artifact retention tetap harus diperhatikan.

---

# 16. Repository Structure

Proposed target structure:

```text
atlas-document-intelligence/
|
├── docs/
│   ├── project-context.md
│   ├── stack-context.md
│   ├── decisions/
│   └── experiments/
|
├── notebooks/
│   └── 01_ml_fundamentals.ipynb
|
├── datasets/
│   ├── samples/
│   └── annotations/
|
├── src/
│   └── atlas_document_intelligence/
│       ├── __init__.py
│       ├── contracts/
│       ├── data/
│       ├── context/
│       ├── inference/
│       ├── extraction/
│       ├── resolution/
│       └── evaluation/
|
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
|
├── experiments/
|
├── pyproject.toml
├── uv.lock
├── .python-version
├── .gitignore
└── README.md
```

Struktur di atas adalah target organisasi repository.

ADI-001 hanya membuat bagian yang benar-benar dibutuhkan untuk menjalankan eksperimen pertama.

Tidak perlu membuat semua folder, class, interface, atau placeholder modules sekaligus.

---

# 17. Stack Activation Matrix

| Instrument | ADI-001 | Later Scope |
|---|---|---|
| Python 3.12 | Active | All |
| uv / pyproject / lockfile | Active | All |
| NumPy / Pandas | Active | As needed |
| Scikit-learn | Active | Baselines |
| Jupyter | Active | Research |
| Pydantic | Active | Contracts |
| pytest / Ruff | Active | All |
| GitHub Actions | Initial lightweight checks | Expanded CI |
| NDV1 adapter | Not yet | ADI-002 |
| PyTorch / Transformers | Not yet | Neural model experiments |
| GLiNER / GLiREL | Not yet | Specialized extraction comparison |
| Vision-Language Models | Not yet | Multimodal experiments |
| ONNX Runtime | Not yet | CPU optimization experiments |
| NetworkX | Not yet | Semantic consolidation |
| Label Studio | Not yet | Annotation workflow if justified |
| MLflow | Not yet | Experiment tracking if justified |
| FastAPI / Uvicorn | Not yet | Atlas integration |
| Graph database | Not yet | Only if proven necessary |

---

# 18. Explicit Architecture Exclusions

The following must not be introduced as baseline dependencies without separate approval:

- Legacy BSS or SEM implementations.
- Docling Graph as the default extraction engine.
- LangChain.
- LangGraph.
- CrewAI.
- Autonomous multi-agent orchestration.
- Neo4j.
- Vector database.
- Celery.
- Redis.
- Kubernetes.
- A new OCR pipeline.
- Production LLM or VLM deployment.
- Direct Atlas database access.
- Mandatory external inference provider.
- Training a foundation model from scratch.

Exclusion does not mean that a technology can never be evaluated.

It means its adoption requires a specific need and separate evidence.

---

# 19. Stack Change Control

Every future Implementation Context must specify:

1. Stack Context version referenced.
2. Active dependencies required.
3. New dependencies, if any.
4. Why existing instruments are insufficient.
5. Expected runtime and hardware.
6. Model identity and license, if applicable.
7. Input and output contracts.
8. Evaluation method.
9. Acceptance criteria.
10. Explicitly excluded work.

Codex must not silently expand the dependency graph or create unused infrastructure.

Any experimental library must remain isolated until its value has been demonstrated.

---

# 20. ADI-001 Stack Boundary

The first implementation scope is:

**ADI-001 — Python & ML Experiment Foundation**

Allowed stack:

```text
Python 3.12
VS Code
venv
uv
pyproject.toml
uv.lock

NumPy
Pandas
Scikit-learn
Matplotlib
Jupyter / ipykernel

Pydantic
pytest
Ruff
GitHub Actions
```

Expected outcome:

- Python project initializes successfully.
- Dependencies resolve reproducibly.
- Python package imports correctly.
- Basic unit tests pass.
- A sample labeled text dataset loads.
- A simple classifier can be trained.
- Model inference produces predictions.
- Evaluation metrics are generated.
- Experiment results can be inspected.
- The experiment runs without GPU or remote provider access.

Not included:

- Production NDV1 integration.
- Semantic relationship extraction engine.
- Multimodal model execution.
- FastAPI server.
- Graph database.
- Fine-tuning.
- Autonomous agents.

**ADI-001 establishes the foundation for measurable AI experiments, not a production inference service.**

---

# 21. Official Technology References

- Python — https://www.python.org/
- uv — https://docs.astral.sh/uv/
- Scikit-learn — https://scikit-learn.org/
- NumPy — https://numpy.org/
- Pandas — https://pandas.pydata.org/
- Pydantic — https://docs.pydantic.dev/
- pytest — https://docs.pytest.org/
- Ruff — https://docs.astral.sh/ruff/
- PyTorch — https://pytorch.org/
- Hugging Face Transformers — https://huggingface.co/docs/transformers/
- ONNX Runtime — https://onnxruntime.ai/
- NetworkX — https://networkx.org/
- MLflow — https://mlflow.org/
- Label Studio — https://labelstud.io/
- FastAPI — https://fastapi.tiangolo.com/

---

# 22. Final Engineering Decision

Atlas Document Intelligence will be developed as:

**A Python-based, CPU-first, model-agnostic multimodal document interpretation and semantic extraction system.**

The project will prioritize:

**Data quality → Ground truth → Baseline inference → Evaluation → Model comparison → Multimodal inference → Semantic consolidation → Production integration.**

Technology adoption must follow demonstrated engineering needs.

No specific pretrained model or framework is assumed to solve semantic interpretation before evaluation proves its capability.

**Next authorized planning activity: ADI-001 Implementation Context, after Stack Context review and approval.**