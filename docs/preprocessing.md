## Coordinate systems

Three representations must not be conflated:

1. **Source-volume space:** the VISCERAL Anatomy3 and IRCAD 3D-IRCADb-01 CT volumes and their registered segmentations.
2. **Model space (Polar Coordinate):** the rectangular simulator output used for training.  Stage I models produce images in this representation.
3. **Display space (Cartesian Coordinate):** a fan-shaped ultrasound view reconstructed from a model-space image after inference. This remapping is a visualization/deployment postprocessing step, not a model input transformation.

## Dataset-construction pipeline

1. Assign each source CT volume to `train`, `validation`, or `test` before generating two-dimensional samples.
2. Standardize the CT orientation, voxel spacing, intensity representation, and associated segmentations.
3. Generate the physics-based ultrasound image by ray casting the CT volume with the selected probe pose and simulator configuration.
4. Sample the semantic volume with the same pose and ray geometry so that the ultrasound image and semantic map are pixel-aligned in model space.
5. Decode semantic colors to integer class IDs using the fixed mapping in [the semantic-label documentation](../data/test_labels/README.md).
6. Apply the model-specific resizing, encoding, normalization, and paired augmentation described below.

## Semantic-label contract

The label space contains 16 mutually exclusive integer values: background (`0`) plus 15 anatomical/pathological classes (`1`--`15`). Their order is:

```text
background, bone, artery, pancreas, gallbladder, kidney, adrenals,
liver, spleen, vein, colon, duodenum, small bowel, stomach,
torso fat, tumor
```

RGB label files use exact color equality. In the retained decoder, a pixel that does not match one of the 15 foreground colors remains class `0`. This includes genuinely unknown labels as well as colors introduced by antialiasing, lossy compression, or continuous interpolation.

(!) Use nearest-neighbor interpolation for every resize, rotation, or coordinate remap of a mask, then colorize it only for visualization. different input dataset.

## Model-specific transforms

| Model/stage | Semantic input | Image input or target | Training augmentation |
| --- | --- | --- | --- |
| Pix2Pix (Stage I) | Decode RGB to class-index channel. Training data were already `256 x 256`, so no loader resize was active. | RGB physics-based target; channel-wise normalization with mean `0.5` and standard deviation `0.5`, giving approximately `[-1, 1]`. | Paired horizontal flip with probability `0.5`; no crop or intensity augmentation. |
| SDM (Stage I) | Decode to class IDs, resize to `128 x 128` with nearest-neighbor interpolation, then one-hot encode into 16 channels. | RGB physics-based target resized to `128 x 128` with bicubic interpolation and mapped from `[0, 255]` to `[-1, 1]`. | Paired horizontal flip with probability `0.5`; random crop disabled. |
| SG-CycleGAN (Stage II) | Not used as a semantic tensor; Stage I US output is the source-domain image. | Grayscale `256 x 256` images normalized from `[0, 255]` to `[-1, 1]`. | Horizontal flip is enabled for training and disabled for evaluation. |


## Cartesian-Coordinate postprocessing

1. Rotate the image by `+90` degrees about pixel center `(63.5, 63.5)` with OpenCV `warpAffine` (linear interpolation and a constant black border by default).
2. Resize the rotated image to width `612` and height `203` with `INTER_AREA`.
3. Add a `333 x 612` black region above and a `103 x 612` black region below, producing a `639 x 612` polar canvas.
4. Apply the inverse polar-to-Cartesian map with `cv2.remap`, `INTER_LINEAR`, and a constant black border.

The serialized transform used by the retained deployment code contains:

| Parameter | Value |
| --- | --- |
| Center | `[340.64201183, 0.0]` |
| Initial radius | `152.57058607350618` |
| Final radius | `508.2178832655416` |
| Initial angle | `-0.5690840699165927` radians |
| Final angle | `2.5690840699165927` radians |
| Polar canvas size | `(639, 612)` |
| Cartesian output size | `(580, 640)` (height, width) |
| Color mode | Enabled |
| Mapping library | `polarTransform 2.0.0` |

(!) This configuration was specifically designed for, and is therefore dependent on, the output format of the physics-based simulator
