# Experimental performance records

## Params
**Pre-trained weights**: R50+ViT-B/16<br>
**Input shape**: (224, 224, 3)<br>
**Encoder trainable**: False<br>
**Batch size**: 24<br>
**Epochs**: 1,290

## Model
**Input Image (224, 224, 3)** → Vision Transformer (224, 224, 32) → **Conv1x1 (224, 224, 1)**

**Total params**: 100,840,640<br>
**Trainable params**: 7,389,312<br>
**Non-trainable params**: 93,451,328<br>

## Dataset

- [Medial Images for Gastric Cancer Diagnosis (Korean)](https://aihub.or.kr/aidata/33988)

### Preprocessing

- [Preprocessors](https://github.com/Basars/preprocessors)

1. Remove malformed images from entire dataset.
2. Fill missing polygons from label data using [CVAT](https://github.com/openvinotoolkit/cvat). (Thanks to @kdh93)
3. Exports its ROI, binary masks and transformed original images.

### Augmentation

Used `tf.data.Dataset` to boost training performance

- Scaling (1.0 / 255)
- Random Flip Left-Right
- Random Flip Up-Down
- Random Crop
- Random Brightness (-0.2 ~ +0.2)
- Gaussian Noise (mean = 0, stddev = 0.05)

## Compile options

### Loss
- BCE * 0.5 + [Dice](https://github.com/Basars/basars-addons/blob/main/basars_addons/losses/dice.py) * 0.5

### Metrics
- [Binary IoU](https://github.com/Basars/basars-addons/blob/main/basars_addons/metrics/intersection_over_union.py)
  - Number of classes = 2 (default)
  - Threshold = 0.5

### Optimizer
- SGD
  - Momentum = 0.9
  - Learning Rate Scheduler
    - [Cosine Annealing Warmup Restarts](https://github.com/Basars/basars-addons/blob/main/basars_addons/schedules/cosine_decay.py)
      - First cycle steps = 100
      - Initial learning rate = 1e-3
      - First decay steps = 300
      - t_mul = 1.0
      - m_mul = 1.0 (default)
      - alpha = 0.0 (default)

Cosine Annealing Warmup Restarts:

![lr_schedule](https://github.com/Basars/segmentation-experiments/blob/main/static/lr_schedule.png)

## Results
![loss_graph](https://github.com/Basars/segmentation-experiments/blob/main/static/loss.png) ![iou_graph](https://github.com/Basars/segmentation-experiments/blob/main/static/iou.png)

### Evaluation
![performance](https://github.com/Basars/segmentation-experiments/blob/main/static/performance.png)