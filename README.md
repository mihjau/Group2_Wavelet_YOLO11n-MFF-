# Wavelet-Enhanced YOLO11n-MFF with a P2 Detection Head for Small Lesion Detection in Chest X-rays

> **Research in progress** · Computer Vision · Object Detection · Medical Imaging

A research implementation exploring whether spatially localized wavelet features and a high-resolution P2 detection head can improve the detection of small pulmonary lesions in chest X-ray images.

> [!IMPORTANT]
> This repository is for academic research only. The system is **not** intended for clinical diagnosis or medical decision-making.

## 1. Overview

Small abnormalities in chest radiographs can occupy only a limited number of pixels and may be difficult to preserve through downsampling. This project investigates an extension of **YOLO11n-MFF** using **Discrete Wavelet Transform (DWT)**-based feature processing and a **P2 detection head** to better retain fine-grained spatial details.

The project is organized around reproducible comparisons rather than a single headline score. All reported results will be tied to an explicit data manifest, evaluation protocol, and model configuration.

## 2. Research Objectives

- **RQ1 — Small-lesion detection:** Does the proposed Wavelet + P2 approach improve small-lesion detection compared with YOLO11n-MFF and standard lightweight YOLO baselines?
- **RQ2 — Frequency representation:** Does a spatially localized DWT-based representation provide benefits over the frequency-processing approach in the reference MFF design?
- **RQ3 — Architectural controls:** Are gains attributable to wavelet-based feature separation rather than simply greater channel capacity or model complexity?

## 3. Proposed Method

The proposed model extends YOLO11n-MFF with:

1. **Wavelet-based feature decomposition:** Use DWT sub-bands to examine low- and high-frequency information while retaining spatial localization.
2. **Feature fusion:** Integrate the decomposed representations into the detection network.
3. **P2 detection head:** Add a higher-resolution prediction scale to investigate the detection of small lesions.
4. **Controlled ablations:** Compare changes against appropriately matched non-wavelet or capacity-controlled alternatives.

**Architecture figure:** To be added at `assets/architecture.png` after the design is finalized.

## 4. Models and Experimental Comparisons

| Model | Role | Status |
|---|---|---|
| YOLO11n | Standard lightweight baseline | Planned / update status |
| YOLO26n | Additional lightweight architecture comparison | Planned / update status |
| YOLO11n-MFF | Reference method to reproduce | Reproduction in progress |
| YOLO11n-MFF-Wavelet (+ P2) | Proposed research model | In development |
| Parameter-/channel-matched non-wavelet variant | Ablation for architectural control | Planned |

> A comparison with YOLO26n alone does not isolate the effect of Wavelet modules. Capacity-matched ablations are needed to evaluate that hypothesis.

## 5. Dataset and Splitting Protocol

**Dataset:** VinDr-CXR / VinBigData Chest X-ray Abnormalities Detection.

- Official release: **15,000 training images** and **3,000 test images**.
- Working subset currently described in the study plan: **4,833 images** = **4,394 abnormal images** + **439 No Finding images**.
- The complete source dataset includes **14 abnormality classes**. The reference paper reportedly evaluates **10 classes**; the final label mapping and evaluation scope must be documented explicitly.
- Planned development split: approximately **80% train / 10% validation / 10% test**, with per-class stratification where feasible. Because an image can contain multiple findings, any multi-label stratification or exception must be recorded.
- **Split first, sample second:** training-only sampling or class weighting must not change the fixed validation/test partitions.

The final study must identify whether its evaluation uses the official held-out test set or an internally held-out subset; these are **different protocols** and must not be mixed in reported comparisons.

**Reproducibility files (to be published):**

- `data/manifests/train.csv`
- `data/manifests/val.csv`
- `data/manifests/test.csv`
- `configs/dataset.yaml`
- `docs/data_protocol.md`

> Do **not** commit original chest X-ray image files or restricted datasets. Obtain data from an authorized source and follow its license and access terms.

## 6. Training Protocol

The reference paper reports the following settings; **these are not asserted to be the settings of every experiment in this repository**:

| Setting | Reported reference setting |
|---|---|
| Epochs | 300 |
| Batch size | 16 |
| Optimizer | SGD |
| Sampling | Weighted sampling (exact implementation to be verified) |
| Input resolution | Must be verified from the original implementation |

For this project's experiments, publish the actual image size, batch size, optimizer, learning-rate schedule, hardware, random seed, sampler implementation, and full configuration in `configs/` and experiment logs.

## 7. Evaluation

The planned analysis includes:

- **Primary:** small-object detection performance (`AP_small` / `mAP_small`), with the precise small-box threshold stated in pixels or relative area.
- **Detection accuracy:** `mAP@0.5`, `mAP@0.5:0.95`, precision, and recall.
- **Sensitivity:** clearly specify whether sensitivity is measured per lesion, image, or class and at which confidence/IoU thresholds.
- **Per-class analysis:** class counts, AP, recall, and false positives where supported by the evaluation protocol.
- **Efficiency:** parameters, FLOPs, inference latency, and evaluation hardware.

**Important:** Do not report ROC-AUC as a standard detector output without defining the conversion from detections to the scoring unit and explaining the evaluation method.

### Results

**No verified final results are published yet.** This table is a reporting template, not a claim of performance.

| Model | mAP@0.5 | mAP@0.5:0.95 | AP_small | Recall / Sensitivity | Params | FLOPs |
|---|---:|---:|---:|---:|---:|---:|
| YOLO11n | TBD | TBD | TBD | TBD | TBD | TBD |
| YOLO26n | TBD | TBD | TBD | TBD | TBD | TBD |
| YOLO11n-MFF | TBD | TBD | TBD | TBD | TBD | TBD |
| YOLO11n-MFF-Wavelet (+ P2) | TBD | TBD | TBD | TBD | TBD | TBD |

## 8. Repository Structure

```text
.
├── README.md
├── requirements.txt           # Add after environment is pinned
├── configs/                   # Dataset, model, and experiment configs
├── data/
│   └── manifests/             # File IDs and train/val/test membership; no images
├── notebooks/                 # EDA and exploratory notebooks
├── src/                       # Model components and reusable training code
├── scripts/                   # Training, evaluation, and preprocessing entrypoints
├── assets/                    # Architecture, EDA, qualitative-result figures
├── results/                   # Metrics, tables, logs (no unapproved patient data)
└── docs/                      # Dataset protocol, experiment protocol, references
```

Create directories as implementation files become available; the structure above is a suggested target, not a statement that all files already exist.

## 9. Reproducing the Study

**Under preparation.** Once the training pipeline is stable, document:

1. Python/CUDA/PyTorch/Ultralytics versions and hardware.
2. How to obtain the dataset through authorized channels.
3. How to generate the exact manifest and verify split checksums.
4. The command and config used for each baseline and ablation.
5. The evaluation command and metric definitions.
6. Links or identifiers for published checkpoints, subject to hosting and licensing requirements.

Do not add `pip install` or `python train.py` commands until those commands are tested in this repository.

## 10. Project Status

- [ ] Freeze data manifests and validate label mapping
- [ ] Complete EDA and publish aggregate statistics
- [ ] Reproduce YOLO11n and YOLO11n-MFF baselines
- [ ] Implement and verify Wavelet module
- [ ] Integrate and assess P2 detection head
- [ ] Run capacity-matched ablations
- [ ] Publish fixed-split evaluation results
- [ ] Finalize paper and reproducibility instructions

Update the checklist only after verifying the corresponding artifact or run.

## 11. References

Add complete bibliographic entries and verified links for:

1. The official VinDr-CXR dataset paper and access page.
2. The original YOLO11n-MFF paper and associated code, if available.
3. Foundational DWT / Wavelet sources used by the implementation.
4. Relevant small-object detection and medical imaging benchmarks.

## 12. Team and Citation

**Team:** [Group name / members / institution]

**Research contact:** [Public contact, optional]

**Citation:** A paper citation / `CITATION.cff` will be added after the manuscript is finalized.

**License:** To be selected after checking the licenses of dependencies, upstream code, and data. Do not assume the dataset and code share the same license.
