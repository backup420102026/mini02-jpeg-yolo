# Mini Research #2 — JPEG Compression and YOLO Object Detection

## Research question

> **Can simple image preprocessing techniques recover YOLO object-detection performance after JPEG compression?**

**Arabic:** هل يمكن لتقنيات بسيطة لمعالجة الصور استعادة أداء YOLO للكشف عن الأجسام بعد ضغط JPEG؟

## Overview

This mini research project studies how JPEG compression affects a pretrained YOLO object detector and whether three simple preprocessing operations can recover part of the lost detection performance.

The experiment uses **20 images from the COCO train2017 dataset**, containing **238 non-crowd annotated objects**. The same ground-truth annotations are used across all image conditions.

### Experimental conditions

**Phase 1 — Compression effect**
1. Original
2. JPEG Quality 70
3. JPEG Quality 50
4. JPEG Quality 30

**Phase 2 — Recovery at the strongest tested compression**
5. JPEG Q30 + Sharpening
6. JPEG Q30 + Denoising
7. JPEG Q30 + 2× Upscaling

The detector is the same pretrained **Ultralytics YOLO26n** model in every condition. No model retraining or fine-tuning is performed.

## Main result

JPEG compression reduced detection performance. Relative to the original images, Q30 produced:

- **mAP50:** 0.6166 → 0.5367 (**−12.95%**)
- **mAP50-95:** 0.4431 → 0.3544 (**−20.02%**)
- **Recall:** 0.3613 → 0.2983 (**−17.44%**)

Among the three recovery methods, **Denoising** performed best overall:

- mAP50: 0.5396 (**+0.55% vs. Q30**)
- mAP50-95: 0.3709 (**+4.65% vs. Q30**)
- Recall: 0.3487 (**+16.90% vs. Q30**)

Sharpening increased Precision but reduced Recall and both mAP measures. Upscaling produced only a small change.

## Repository structure

```text
mini02_jpeg_yolo_FINAL/
├── notebooks/
│   └── mini02_jpeg_yolo.ipynb
├── reports/
│   ├── mini02_research_paper.md
│   └── mini02_research_paper.pdf
├── results/
│   ├── mini02_all_results.csv
│   ├── mini02_recovery_analysis.csv
│   └── mini02_per_image_summary.csv
├── figures/
│   └── README.md
├── data/
│   └── README.md
├── requirements.txt
├── .gitignore
└── README.md
```

## Notebook

The notebook is organized as a complete research workflow:
1. Research question and design
2. Environment and imports
3. Dataset checks
4. JPEG generation
5. YOLO inference
6. Ground-truth loading
7. IoU matching
8. Precision / Recall
9. AP and mAP50 / mAP50-95
10. Q30 preprocessing
11. Recovery evaluation
12. Per-image sanity analysis
13. Final tables
14. Final plots
15. Export of result CSV files

The notebook expects the prepared dataset at `/content/mini02_dataset/`.

## Data

The selected COCO images are intentionally not bundled. See `data/README.md` for the expected structure and reconstruction notes.

## Methodological note

This is a **small-scale experimental study**, not a replacement for a full COCO benchmark. The reported mAP values are study-specific values calculated by the notebook's explicit evaluation implementation over the object classes present in the selected subset.

## References

1. Lin, T.-Y., et al. (2014). *Microsoft COCO: Common Objects in Context*. ECCV.
2. Redmon, J., Divvala, S., Girshick, R., & Farhadi, A. (2016). *You Only Look Once: Unified, Real-Time Object Detection*. CVPR.
3. Ultralytics. *YOLO26 Documentation and Python Usage*. Official Ultralytics documentation.

## Status

**Mini Research #2 — COMPLETE**
