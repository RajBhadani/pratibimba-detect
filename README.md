# TATHYA — Tamper And THreat Intelligence analYsis Architecture

> Real-Time AI Deepfake Detection for Operational Environments  
> **iDEX DIO Open Challenge 2026** | Raj Bhadani, Deshbandhu College, University of Delhi

[![Status](https://img.shields.io/badge/Status-In%20Development-orange)]()
[![Challenge](https://img.shields.io/badge/iDEX%20DIO-2026-blue)]()
[![Internship](https://img.shields.io/badge/Research-NIT%20Kurukshetra%202026-green)]()

---

## The Problem

AI-generated disinformation targeting Indian military operations reaches **10M+ users before official rebuttal** — with a detection lag exceeding **3 hours**. In incidents such as Galwan (June 2020) and Pahalgam (April 2025), synthetic media spread unchecked for hours before any correction was issued.

| Incident | Reach Before Rebuttal | Detection Lag | Documented Harm |
|----------|----------------------|---------------|-----------------|
| Galwan, June 2020 | 10M+ users | > 3 hours | Casualty count manipulation |
| Pahalgam, April 2025 | 10M+ users | > 3 hours | Op security breach narrative |

Existing deepfake detectors make this worse — trained on clean benchmark data, they fail the moment video passes through WhatsApp, YouTube, or Twitter compression. **XceptionNet drops from 0.997 AUC on FaceForensics++ to 0.482 on Celeb-DF** — below chance — because social media compression destroys the pixel-level artifacts these models rely on.

TATHYA is being built to work in the real world, not the lab.

---

## System Architecture

```
Input Media (image / video / audio)
        │
        ▼
┌───────────────────────────────────────────┐
│                  DETECT                   │
│  Binary classification — real or synthetic│
│  FaceForensics++ fine-tuned CNN           │
│  + compression-aware augmentation         │
│  + frequency-domain (DCT) feature branch  │
│  Target: >75% accuracy | <120s inference  │
└──────────────────┬────────────────────────┘
                   │
                   ▼
┌───────────────────────────────────────────┐
│                  TRACE                    │
│  Source attribution + propagation mapping │
│  GAN fingerprinting + Social Network      │
│  Analysis (NetworkX — betweenness &       │
│  eigenvector centrality)                  │
│  Target: Origin node ID within 5 min      │
└──────────────────┬────────────────────────┘
                   │
                   ▼
┌───────────────────────────────────────────┐
│                  ALERT                    │
│  Tiered notification pipeline             │
│  Auto-flag (>95%) → Review (70–95%)       │
│  → Log (<70%)                             │
│  Target: <8 minutes end-to-end latency    │
└───────────────────────────────────────────┘
```

---

## Current Development Status

> This project is under active development as part of iDEX DIO 2026 and a Summer Research Internship at NIT Kurukshetra (June–July 2026) under Dr. Nidhi Gupta, Department of Computer Applications.

| Module | Status | Description |
|--------|--------|-------------|
| DETECT — Baseline | 🔄 In Progress | XceptionNet on FF++ — confirming accuracy drop under compression |
| DETECT — Augmentation | 📋 Planned | Compression-aware training (JPEG q40–90, H.264 CRF 23–40) |
| DETECT — Frequency branch | 📋 Planned | DCT coefficient map fusion alongside spatial features |
| TRACE — Propagation graph | 📋 Planned | NetworkX SNA on social media datasets |
| ALERT — Pipeline | 📋 Planned | Tiered alert logic with confidence thresholds |

---

## Research Foundation

**Core gap:** CNN-based detectors degrade 15–18% on cross-dataset benchmarks and fail critically on social media-compressed content. Three modifications address this:

**1. Compression-aware augmentation**  
Training frames randomly re-encoded at JPEG quality 40–90 and H.264 CRF 23–40 — simulating WhatsApp, YouTube, and Twitter compression profiles.

**2. Frequency-domain feature branch**  
DCT coefficient maps fed as a secondary input stream. Frequency residuals survive compression better than spatial edge artifacts.

**3. Compression-robustness loss**  
Binary cross-entropy combined with a regularizer penalising features that degrade between clean and compressed versions of the same frame.

**Key references:**
- Rössler et al. (2019). FaceForensics++. ICCV. [arxiv.org/abs/1901.08971](https://arxiv.org/abs/1901.08971)
- Zhang et al. (2024). Face Forgery Detection — Fine-Grained Clues and Noise Inconsistency. IEEE TCSVT. [10.1109/TCSVT.2024.10669058](https://doi.org/10.1109/TCSVT.2024.10669058)
- Yang et al. (2024). CSTAN. Sensors (MDPI). [10.3390/s24227101](https://doi.org/10.3390/s24227101)
- Li et al. (2025). PLADA. [arXiv:2506.20548](https://arxiv.org/abs/2506.20548)

---

## Performance Targets

| Module | Metric | Target |
|--------|--------|--------|
| DETECT | Accuracy on clean data | > 75% |
| DETECT | AUC on c40 compressed variant | > 85% |
| DETECT | Cross-dataset AUC (Celeb-DF-v2) | > 80% |
| DETECT | Inference time per clip | < 120s |
| TRACE | Origin node identification | Within 5 min |
| ALERT | End-to-end latency | < 8 min |

---

## Repository Structure

```
tathya-detect/
├── detect/
│   ├── model.py                    # Classifier architecture
│   ├── train.py                    # Training pipeline
│   └── inference.py                # Single image/video inference
├── trace/
│   └── propagation.py              # NetworkX SNA — source attribution
├── alert/
│   └── pipeline.py                 # Tiered alert logic
├── notebooks/
│   └── baseline_xception.ipynb     # Baseline + compression drop analysis
├── data/
│   └── README.md                   # FF++ dataset setup
├── docs/
│   └── TATHYA_Project_Summary.pdf
└── requirements.txt
```

---

## Quickstart

```bash
git clone https://github.com/rajbhadani/tathya-detect
cd tathya-detect
pip install -r requirements.txt

# Single image inference
python detect/inference.py --input path/to/image.jpg

# Video inference
python detect/inference.py --input path/to/video.mp4 --mode video
```

---

## Dataset Setup

TATHYA trains on [FaceForensics++](https://github.com/ondyari/FaceForensics).

1. Request access: https://github.com/ondyari/FaceForensics
2. Download compressed variants c23 and c40
3. Cross-dataset evaluation: [Celeb-DF-v2](https://github.com/yuezunli/celeb-deepfakeforensics) and [DFDC-Preview](https://ai.facebook.com/datasets/dfdc/)

See `data/README.md` for folder structure.

---

## Operational Validation Request

If you are a serving or retired defence or intelligence professional, I am seeking responses to two specific questions for the iDEX threat model:

1. Do coordinated disinformation campaigns targeting active operations constitute a ground-level threat — and did the 3+ hour detection lag create measurable operational vulnerability?
2. Would an 8-minute detection-to-alert pipeline have practical effect on that vulnerability, or is the decision cycle too fast or too slow for this latency to matter?

A 15-minute call or written response is sufficient. Your input would be cited as operational validation and formally acknowledged in the iDEX submission.

📧 rajbhadani9897@gmail.com | 📞 +91 9572768016

---

## Author

**Raj Bhadani**  
B.Sc. (Hons) Computer Science,

Deshbandhu College, University of Delhi  
[rajbhadani9897@gmail.com](mailto:rajbhadani9897@gmail.com) | [LinkedIn](https://linkedin.com/in/raj-bhadani-b4b729258) | [GitHub](https://github.com/RajBhadani)

---

*iDEX DIO Open Challenge 2026 | June 2026*
