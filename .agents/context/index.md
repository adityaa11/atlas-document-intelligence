# Project Context Index

- [Inference Engine Project Context](project.md)

Status: ready  
Last reviewed: 2026-11-01

Project summary:

Inference Engine generic dan multimodal untuk menerima `NormalizedDocument/v1`,
mengekstrak interpretation serta relation dari text, table, dan PNG hasil
perception, lalu menghasilkan evidence, source reference, confidence, dan
ambiguity. Engine dipanggil oleh background service melalui API contract yang
identity-aware, dengan per-service credential pada development, mTLS sebagai
target production, authorization policy di Fastify, dan audit log wajib.

Project goals juga mencakup workflow group, fact group, relation many-to-many
antara workflow dan fact, serta retrieval-ready data, index, dan API. Workflow
dan facts adalah output semantic utama yang dikelompokkan berdasarkan makna;
chatbot berada di luar scope. UI demo menjadi reference consumer untuk
memahami penyajian dan navigasi knowledge, bukan deliverable engine.
Conflict detection juga termasuk scope: engine harus mempertahankan claim yang
bertentangan, menandainya sebagai unresolved, dan menyajikan evidence untuk
resolution oleh user atau authority yang ditunjuk.

Primary boundary:

PDF mentah dan proses perception berada di luar runtime Inference Engine.
Chatbot dan UI demo juga berada di luar runtime engine; retrieval-ready data,
index, dan API berada di dalam scope.
Conflict detection dan unresolved conflict records berada di dalam scope.
PNG hasil perception menjadi input pendukung melalui `visualRegions.assetRef`.
Hanya background service yang terautentikasi dan terotorisasi yang boleh
memanggil engine.
