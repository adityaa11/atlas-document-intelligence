# Project Context Index

- [Inference Engine Project Context](project.md)

Status: ready  
Last reviewed: 2026-10-31

Project summary:

Inference Engine generic dan multimodal untuk menerima `NormalizedDocument/v1`,
mengekstrak interpretation serta relation dari text, table, dan PNG hasil
perception, lalu menghasilkan evidence, source reference, confidence, dan
ambiguity.

Primary boundary:

PDF mentah dan proses perception berada di luar runtime Inference Engine.
PNG hasil perception menjadi input pendukung melalui `visualRegions.assetRef`.
