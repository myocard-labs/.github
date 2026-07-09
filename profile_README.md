# myocard-labs

> Open, reproducible machine-learning tooling for **intracardiac electrogram (EGM)** analysis —
> detecting fibrotic atrial tissue to help target catheter ablation for **atrial fibrillation (AF)**.

*A personal research & portfolio project by **Daniel Klein**, a sensor- and signal-processing
engineer moving from aerospace/defense sensing into medical devices.*

---

## Why this exists

Catheter ablation for AF works best when the fibrotic tissue driving the arrhythmia is located
accurately. Today that substrate is inferred mostly from bipolar voltage amplitude, which is noisy
and operator-dependent. **myocard-labs** asks a focused question: *can a model read the shape of an
intracardiac electrogram and flag fibrotic tissue directly?*

It's also a deliberate demonstration piece — porting a decade of sensor/signal-processing and ML
experience (radar, SAR, hyperspectral, RF) onto a physiological signal, with medical-device-style
engineering discipline.

## How it's built — and my role

This project is **built with heavy use of AI coding assistants.** My contribution is the engineering
judgment around them: I set the research direction from the clinical/ML literature, adapted the model
architecture to the task, designed the data schemas and system architecture, and reviewed and
validated the generated code — architect / ML lead / reviewer rather than line-by-line author.

**Pipeline:** simulate 2-D atrial tissue with variable fibrosis → extract pseudo-bipolar EGMs →
overlay real recording noise (PhysioNet IAFDB) → train a **1-D MobileViT (CNN + transformer)**
per-trace classifier → export a temperature-calibrated **ONNX** model.

## Where it stands — honestly

**Phase 1:** the classifier reaches ~0.999 validation AUROC *in-distribution*. But a **label-free test
on real IAFDB recordings shows the decision function saturates** — it calls almost everything fibrotic.
That's an informative negative result: the synthetic training distribution is too easy, not a
calibration problem. Current work (Phase 1.5) makes the simulation more physically realistic (human
atrial ionic cell model, physiological noise) to close that gap. Reporting the negative result plainly
is the point.

## The repositories

**Foundation (shared libraries)**
- [`egm-contracts`](https://github.com/myocard-labs/egm-contracts) — JSON-Schema source of truth for every data format; typed models generated from it.
- [`egm-data`](https://github.com/myocard-labs/egm-data) — the I/O layer (HDF5 / CSV / JSON) for every bank and record.
- [`egm-signal`](https://github.com/myocard-labs/egm-signal) — DSP primitives: filtering, calibration, segmentation, temperature scaling.
- [`egm-features`](https://github.com/myocard-labs/egm-features) — per-trace morphology / spectral / complexity feature extraction.

**Producers (generate data)**
- [`iafdb-pipeline`](https://github.com/myocard-labs/iafdb-pipeline) — processes the PhysioNet IAFDB into healthy-segment and noise banks.
- [`synthetic-egm-pipeline`](https://github.com/myocard-labs/synthetic-egm-pipeline) — atrial simulation → pseudo-bipolar EGM extraction → realistic-noise mixing.

**Consumers (apps)**
- [`egm-classifier`](https://github.com/myocard-labs/egm-classifier) — the 1-D MobileViT classifier: train / evaluate / export (ONNX + calibration).
- [`egm-studio`](https://github.com/myocard-labs/egm-studio) — desktop GUI for signal exploration, ML diagnostics, and publication figures.

**Project**
- [`intracardiac-platform`](https://github.com/myocard-labs/intracardiac-platform) — **start here:** the meta repo that ties it together (plan, docs, reproducibility index).
- [`intracardiac-papers`](https://github.com/myocard-labs/intracardiac-papers) — LaTeX source for the write-ups.

## Engineering practices

JSON-Schema-first data contracts · typed models at every boundary · independent semantic versioning ·
GitHub Actions CI (lint / type-check / test / build) · cross-repo integration tests · a cross-artifact
provenance index for full experiment traceability.

## Roadmap

Multi-class severity (healthy / border-zone / dense scar) → fibrosis-pattern classification →
3-D atrial substrate & catheter modeling → TensorRT / C++ deployment.

---

*Questions or opportunities: **daniel.anthony.klein.88@gmail.com***


<!-- ============================================================
     SETUP NOTES  (delete this block before publishing — it is
     guidance for you, not part of the public README)
     ============================================================

WHERE THIS GOES (GitHub org profile README):
  1. In the myocard-labs org, create a PUBLIC repo named exactly ".github"
  2. Add a file at path:  profile/README.md
  3. Paste everything ABOVE this comment block. It renders on
     https://github.com/myocard-labs

MAKE SURE the repos you link/pin are PUBLIC, or the links 404 for visitors.

SUGGESTED PINNED REPOS (org page → "Customize pins", max 6), in order:
  1. intracardiac-platform   (entry point / overview)
  2. egm-classifier          (the ML model)
  3. egm-studio              (the GUI — add a screenshot to its README)
  4. synthetic-egm-pipeline  (the data generator)
  5. egm-contracts           (shows the schema-first discipline)
  6. egm-features            (or iafdb-pipeline)

SUGGESTED ONE-LINE REPO DESCRIPTIONS (each repo → About → Description):
  intracardiac-platform : Meta repo + docs for the intracardiac-EGM fibrosis-detection project.
  egm-classifier        : 1-D MobileViT classifier for fibrosis in intracardiac EGMs (train/eval/ONNX export).
  egm-studio            : PySide6 desktop app for EGM signal exploration, ML diagnostics, and figures.
  synthetic-egm-pipeline: Atrial-tissue simulation -> pseudo-bipolar EGMs -> realistic-noise mixing.
  iafdb-pipeline        : PhysioNet IAFDB -> calibrated healthy-segment and noise banks.
  egm-contracts         : JSON-Schema data contracts + generated typed models for the whole pipeline.
  egm-data              : Typed I/O layer (HDF5/CSV/JSON) for all banks and records.
  egm-signal            : Shared DSP primitives (filtering, calibration, segmentation, temperature scaling).
  egm-features          : Per-trace morphology / spectral / complexity feature extraction.
  intracardiac-papers   : LaTeX source for the project write-ups.

QUICK WINS that make the org look finished:
  - Add 1-2 screenshots to egm-studio's README.
  - Add a small pipeline diagram to intracardiac-platform's README.
  - Give every repo a one-line description + topics (tags) like
    machine-learning, signal-processing, medical-devices, electrophysiology.
============================================================ -->
