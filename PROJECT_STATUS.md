# Project Status

## Mini Research #2 — COMPLETE

**Question:** Can simple image preprocessing techniques recover YOLO object-detection performance after JPEG compression?

**Dataset:** 20 COCO images, 238 ground-truth objects.

**Conditions:** Original, Q70, Q50, Q30, Q30+Sharpen, Q30+Denoise, Q30+Upscale.

**Best recovery method:** Denoising.

**Conclusion:** Denoising recovered a limited portion of Q30 performance but did not restore original performance.
