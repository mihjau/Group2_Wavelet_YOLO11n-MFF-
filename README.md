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

## 4. Models and Performance Comparison

### 4.1. Experimental models

| Model | Role | Current status |
|---|---|---|
| YOLO11n | Lightweight baseline | Evaluated at 1024 × 1024 (see Section 4.3) |
| YOLO26n | Additional architecture comparison | Planned; results not yet reported |
| YOLO11n-MFF | Reference method for reproduction | Reproduction in progress; results not yet reported |
| Wavelet-YOLO11n-P2 | Proposed variant named in the current results table | Evaluated at 1024 × 1024 (see Section 4.3) |
| Parameter-/channel-matched non-wavelet variant | Ablation to isolate the impact of wavelet processing | Planned; results not yet reported |

**Model naming to confirm:** The experimental results table labels the proposed model `Wavelet-YOLO11n-P2`, whereas the broader project description uses `YOLO11n-MFF-Wavelet (+ P2)`. Confirm that these refer to the same implemented architecture before using the names interchangeably.

### 4.2. Results reported in prior studies

*Values transcribed from the group's comparison table; original papers and evaluation protocols have not been independently audited here.*

| Model | Input size | Parameters (M) | FLOPs (G) | FPS | mAP@0.5 (%) | mAP@0.5:0.95 (%) |
|---|---|---:|---:|---:|---:|---:|
| CXR-RefineDet [1] | 512 × 512 | 34.20 | 42.5 | 6.8 | 16.90 | 8.40 |
| YOLOv8n Baseline [13] | 640 × 640 | 3.01 | 8.1 | 82.5 | 33.80 | 18.20 |
| YOLO-CXR [8] | 640 × 640 | 11.20 | 28.6 | 55.0 | 33.80 | — |
| Sensitivity-YOLOv11 [6] | 640 × 640 | 20.10 | 68.5 | 45.0 | 38.70 | — |
| YOLOv5nu [4] | 640 × 640 | 1.80 | 4.2 | 95.0 | 38.80 | 20.70 |
| YOLO11n Baseline (PLOS ONE) [4] | 640 × 640 | 2.59 | 6.4 | 78.1 | 38.80 | 21.40 |
| CEFEN-AFFN (Sci Rep 2026) [5] | 640 × 640 | — | — | — | 40.10 | 19.60 |
| YOLOv11-MFF (PLOS ONE 2025) [4] | 640 × 640 | 3.10 | 9.9 | 64.5 | 41.50 | 22.60 |
| NSEC-YOLO (JRRAS 2024) [7] | 640 × 640 | 2.95 | 8.7 | 163.0 | 41.60 | — |

`—` means that the value is not reported in the supplied comparison table. Bracketed reference numbers correspond to the group's manuscript and should be linked to complete bibliographic entries.

### 4.3. Results from our experiments (1024 × 1024)

| Model | Input size | Parameters (M) | FLOPs (G) | FPS | mAP@0.5 (%) | mAP@0.5:0.95 (%) |
|---|---|---:|---:|---:|---:|---:|
| **YOLO11n Baseline** | 512 × 512 | 2.59 | 6.4† | 78.1† | **39.75** | **20.37** |
| Wavelet-YOLO11n-P2 (proposed) | 1024 × 1024 | 2.68 | 31.35 | 41.7 | 37.61 | 19.12 |

**Current observation:** The proposed model scores **2.14 percentage points lower** on mAP@0.5 and **1.25 percentage points lower** on mAP@0.5:0.95 than the team's YOLO11n baseline. The current results therefore **do not demonstrate an overall mAP improvement**. Small-lesion AP (`AP_small`), per-class sensitivity and controlled ablations still need to be reported to evaluate the research hypotheses.

† **Verification required:** The 1024 × 1024 baseline row repeats the **6.4 G FLOPs** and **78.1 FPS** shown for the 640 × 640 paper baseline. These efficiency numbers should be recalculated/measured from the actual 1024 × 1024 inference run and common hardware before being used in any efficiency comparison. The reported values are preserved here exactly as shown in the supplied table, not presented as independently verified measurements.

**Comparability:** Prior-study rows use different input resolutions (512 or 640) from the group's 1024-resolution experiments; reported FPS may also depend on hardware, inference setup, test split and annotation protocol. Do **not** treat the combined table as a controlled head-to-head benchmark without matching these conditions. The use of multi-radiologist consensus label filtering, described in the group's report, must be documented in the evaluation protocol and applied consistently for a fair internal comparison.

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

### Results and reporting status

Numerical performance comparisons are presented in **Section 4.2** (prior studies) and **Section 4.3** (the group's 1024 × 1024 experiments). Metrics for **YOLO26n**, a reproduced **YOLO11n-MFF** baseline, and a **parameter-/channel-matched non-wavelet ablation** are not yet included in the supplied results table.

The research questions about **small pulmonary lesions** cannot be resolved from overall mAP alone: report `AP_small`, a stated box-size criterion, per-class recall/sensitivity, and the fixed test-set protocol when those outputs are available.

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


## 19. Team and Citation

**Team:** [Group 2 / Doan Ngoc Minh Chau-Le Viet Ngoc Hoan / HuongDB2]

**Research contact:** [chaudnm1108@gmail.com, blockgaming369@gmail.com]


