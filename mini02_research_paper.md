# Can Simple Image Preprocessing Recover YOLO Object-Detection Performance After JPEG Compression?

## Mini Research #2 — Final Research Paper

### Abstract

JPEG compression is widely used to reduce image storage and transmission costs, but lossy compression can remove or distort visual information that object-detection systems rely on. This study investigates whether simple image preprocessing operations can recover detection performance after JPEG compression. A pretrained Ultralytics YOLO26n detector was evaluated on a controlled subset of 20 images selected from the COCO train2017 dataset, containing 238 non-crowd annotated objects. Four image-quality conditions were first compared: the original images and JPEG-compressed versions at quality factors 70, 50, and 30. The strongest tested compression level, Q30, was then processed using sharpening, denoising, and 2× Lanczos upscaling. Detection performance was evaluated using Precision, Recall, mAP50, and mAP50-95 under a consistent evaluation procedure.

JPEG compression produced a measurable degradation in detection performance. Relative to the original images, Q30 reduced mAP50 from 0.6166 to 0.5367 (12.95%), mAP50-95 from 0.4431 to 0.3544 (20.02%), and Recall from 0.3613 to 0.2983 (17.44%). Among the recovery methods, denoising produced the strongest overall recovery: mAP50 increased slightly from 0.5367 to 0.5396 and mAP50-95 increased from 0.3544 to 0.3709, while Recall increased from 0.2983 to 0.3487. Sharpening increased Precision but substantially reduced Recall and both mAP measures, whereas upscaling produced only a small change. These findings suggest that simple preprocessing does not universally restore detector performance after JPEG compression; however, denoising may recover a limited portion of the lost performance under the tested conditions.

**Keywords:** JPEG compression, object detection, YOLO, image preprocessing, denoising, sharpening, upscaling, mAP, computer vision.

---

## 1. Introduction

Modern computer-vision systems frequently operate on images that have been compressed for storage, transmission, web delivery, or deployment on resource-constrained devices. JPEG is particularly common because it can substantially reduce file size at the cost of information loss. Although such losses may be visually acceptable to a human observer, object detectors can respond differently because detection depends on local edges, textures, object boundaries, and other image features.

YOLO-family detectors formulate object detection as a unified prediction problem in which object locations and class probabilities are predicted directly from the input image. The original YOLO formulation demonstrated the feasibility of real-time object detection with a single neural network, and later generations have continued to improve the accuracy-speed trade-off [2]. The present study uses the pretrained Ultralytics YOLO26n detector as a fixed detector rather than training a new model. Ultralytics documents YOLO26n as a COCO-pretrained detection model and provides prediction and validation interfaces for quantitative evaluation [3].

The central question is whether simple image preprocessing can compensate for information lost during JPEG compression. Rather than assuming that preprocessing must improve performance, the experiment explicitly tests three plausible but different strategies: sharpening, denoising, and upscaling.

The study is deliberately small and controlled. It is therefore intended as a focused experimental investigation rather than a claim about all image datasets, all JPEG encoders, or all object-detection architectures.

---

## 2. Research Question

> **Can simple image preprocessing techniques recover YOLO object-detection performance after JPEG compression?**

The investigation is divided into two related questions:

1. How does increasing JPEG compression affect YOLO detection performance?
2. At the strongest tested compression level, can simple preprocessing recover some of the lost performance?

---

## 3. Experimental Hypotheses

### H1 — Compression effect
Increasing JPEG compression, particularly at Q30, will reduce object-detection performance relative to the original images.

### H2 — Recovery effect
At Q30, at least one of the tested preprocessing methods will recover part of the performance lost due to compression.

### H3 — Method-specific behavior
Different preprocessing operations will affect Precision and Recall differently; therefore, an increase in one metric will not automatically imply an improvement in overall detection quality.

---

## 4. Dataset

The experiment uses a selected subset of **20 images from COCO train2017**. COCO was designed for object recognition and scene understanding in natural contexts and contains object-level annotations for complex everyday scenes [1].

The selected subset contains:

- **20 images**
- **238 non-crowd ground-truth bounding boxes**
- Multiple object categories
- Variable image dimensions
- Both simple and visually crowded scenes

The original COCO bounding boxes were converted to normalized YOLO-format labels. Because JPEG compression, sharpening, and denoising do not change object geometry, the same normalized ground-truth boxes were reused across those conditions. For upscaling, the normalized coordinates remain geometrically equivalent even though the pixel dimensions increase.

The dataset is intentionally small. This makes the experiment manageable as a mini research project but also limits the generality and statistical power of the conclusions.

---

## 5. Detector and Evaluation Setup

The detector was a **pretrained Ultralytics YOLO26n** model. No training, fine-tuning, or parameter optimization was performed during this experiment. The same checkpoint and inference procedure were used across all conditions.

The main detection metrics were:

- **Precision:** proportion of predicted detections that were correct.
- **Recall:** proportion of ground-truth objects successfully detected.
- **mAP50:** mean Average Precision at IoU = 0.50.
- **mAP50-95:** mean Average Precision averaged across IoU thresholds from 0.50 to 0.95.

True positives, false positives, and false negatives were matched using class agreement and an IoU threshold of 0.50 for the fixed-threshold Precision/Recall analysis.

For AP computation, predictions were retained at a very low confidence threshold and ranked by confidence before constructing precision-recall relationships. AP was then computed for each object class present in the selected subset and macro-averaged across those classes. The mAP values in this report are therefore **study-specific evaluation values**, not official COCO benchmark scores.

---

## 6. Experimental Design

### Phase 1 — Measuring the effect of JPEG compression

| Condition | Description |
|---|---|
| Original | Original image |
| Q70 | JPEG quality = 70 |
| Q50 | JPEG quality = 50 |
| Q30 | JPEG quality = 30 |

### Phase 2 — Attempting recovery

Only Q30 was used for the recovery experiment.

| Condition | Processing |
|---|---|
| Q30 + Sharpen | PIL UnsharpMask |
| Q30 + Denoise | OpenCV Fast Non-Local Means Denoising |
| Q30 + Upscale | 2× Lanczos upscaling |

---

## 7. Results

### 7.1 Effect of JPEG compression

| Condition | TP | FP | FN | Precision | Recall | mAP50 | mAP50-95 |
|---|---:|---:|---:|---:|---:|---:|---:|
| Original | 86 | 22 | 152 | 0.7963 | 0.3613 | **0.6166** | **0.4431** |
| Q70 | 80 | 23 | 158 | 0.7767 | 0.3361 | 0.5717 | 0.4132 |
| Q50 | 84 | 23 | 154 | 0.7850 | 0.3529 | 0.5842 | 0.4075 |
| Q30 | 71 | 17 | 167 | 0.8068 | 0.2983 | 0.5367 | 0.3544 |

Relative to Original, Q30 produced:

- Precision: **+1.32%**
- Recall: **−17.44%**
- mAP50: **−12.95%**
- mAP50-95: **−20.02%**

The increase in Precision should not be interpreted as an overall improvement. Q30 produced fewer true positives and therefore missed more annotated objects.

### 7.2 Recovery after Q30

| Condition | Precision | Recall | mAP50 | mAP50-95 |
|---|---:|---:|---:|---:|
| Q30 | 0.8068 | 0.2983 | 0.5367 | 0.3544 |
| Q30 + Sharpen | **0.8462** | 0.2311 | 0.4736 | 0.2895 |
| Q30 + Denoise | 0.7905 | **0.3487** | **0.5396** | **0.3709** |
| Q30 + Upscale | 0.7935 | 0.3067 | 0.5324 | 0.3497 |

#### Sharpening
Sharpening increased Precision by 4.88% relative to Q30 but decreased Recall by 22.54%, mAP50 by 11.75%, and mAP50-95 by 18.32%. It therefore did not recover overall detector performance.

#### Denoising
Denoising was the strongest recovery method. Relative to Q30, it increased Recall by 16.90%, mAP50 by 0.55%, and mAP50-95 by 4.65%. The gain was meaningful in direction but incomplete: performance remained below the Original condition.

#### Upscaling
Upscaling produced only small changes: Recall +2.82%, mAP50 −0.81%, and mAP50-95 −1.32% relative to Q30. It did not provide useful recovery.

---

## 8. Per-Image Sanity Analysis

| Condition | Mean Precision | Precision SD | Mean Recall | Recall SD |
|---|---:|---:|---:|---:|
| Original | 0.8105 | 0.1816 | 0.4381 | 0.2270 |
| Q70 | 0.7127 | 0.2611 | 0.4193 | 0.2563 |
| Q50 | 0.7684 | 0.1928 | 0.4293 | 0.2491 |
| Q30 | 0.7924 | 0.2584 | 0.3636 | 0.2534 |
| Q30 + Sharpen | 0.7942 | 0.3600 | 0.2747 | 0.2508 |
| Q30 + Denoise | **0.8057** | 0.2124 | **0.4052** | 0.2471 |
| Q30 + Upscale | 0.7786 | 0.2658 | 0.3661 | 0.2528 |

The per-image analysis preserves the main qualitative pattern of the aggregate results. The relatively large standard deviations also show why the conclusions should remain cautious given the small sample.

---

## 9. Discussion

The first phase supports the expectation that strong JPEG compression can reduce object-detection performance. Q30 produced a substantial reduction in Recall and both mAP measures compared with the original images. The larger decline in mAP50-95 suggests that localization quality across stricter IoU thresholds may be particularly sensitive to compression.

The slight increase in Precision at Q30 is informative: compression did not simply cause the detector to generate more false positives. Instead, the detector became less successful at recovering all ground-truth objects.

The preprocessing results show that recovery is not a universal image-enhancement effect. Sharpening produced stronger edges but did not restore detector performance. Denoising produced the clearest recovery, especially in Recall, possibly because some compression-related artifacts were suppressed. Upscaling was largely ineffective because interpolation cannot reconstruct information that compression removed.

Overall, H2 is supported only in a limited form: **denoising recovered a small portion of the performance lost under Q30**. The stronger claim that preprocessing generally restores YOLO performance is not supported.

---

## 10. Limitations

1. **Small sample size:** 20 images and 238 objects are appropriate for a mini study but insufficient for broad generalization.
2. **Subset selection:** the images are a selected COCO subset rather than a full benchmark.
3. **Single detector:** only YOLO26n was tested.
4. **Fixed preprocessing settings:** each method used one parameter configuration.
5. **Single JPEG encoding:** repeated compression and alternative encoders were not tested.
6. **No formal statistical significance testing:** the study is exploratory.
7. **Study-specific evaluation:** the mAP values should not be interpreted as official COCO benchmark results.

---

## 11. Conclusion

This study investigated whether simple preprocessing methods can recover YOLO object-detection performance after JPEG compression.

Strong JPEG compression reduced performance. At Q30, mAP50 decreased by approximately 13%, mAP50-95 by approximately 20%, and Recall by approximately 17% relative to the original images.

Of the three recovery techniques, **denoising produced the strongest recovery**, increasing Recall by approximately 16.9% and mAP50-95 by approximately 4.65% relative to Q30. However, it did not restore original performance.

Sharpening increased Precision but harmed Recall and both mAP metrics. Upscaling produced only a small change and did not recover the lost mAP.

> **Under the tested conditions, simple preprocessing can recover a limited portion of YOLO detection performance after JPEG compression, but recovery is method-dependent and incomplete. Denoising was the most promising of the three tested methods.**

These conclusions apply specifically to the selected COCO subset, the YOLO26n pretrained detector, the tested JPEG quality levels, and the preprocessing parameters used in the experiment.

---

## 12. Reproducibility

The accompanying repository contains the complete research notebook, exact preprocessing parameters, evaluation code, result tables, paper, and figure naming convention.

The dataset itself is not redistributed in the repository.

---

## References

[1] Lin, T.-Y., Maire, M., Belongie, S., Bourdev, L., Girshick, R., Hays, J., Perona, P., Ramanan, D., Zitnick, C. L., & Dollár, P. (2014). *Microsoft COCO: Common Objects in Context*. ECCV.

[2] Redmon, J., Divvala, S., Girshick, R., & Farhadi, A. (2016). *You Only Look Once: Unified, Real-Time Object Detection*. CVPR.

[3] Ultralytics. *YOLO26 Documentation and Python Usage*. Official Ultralytics documentation.
