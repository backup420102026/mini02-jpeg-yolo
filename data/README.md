# Dataset

The experiment uses a selected subset of **20 images from COCO train2017** with **238 non-crowd ground-truth bounding boxes**.

Expected prepared structure:

```text
/content/mini02_dataset/
├── images/
├── labels/
├── images_q70/
├── images_q50/
└── images_q30/
```

YOLO labels use normalized `class_id x_center y_center width height` format.

The original COCO annotation source was `instances_train2017.json`. The dataset files are not bundled in this GitHub package.
