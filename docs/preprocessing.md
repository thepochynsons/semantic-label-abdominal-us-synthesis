# Preprocessing

This document records the preprocessing contract used by the experiments. Implementation-specific scripts are not distributed with this repository.

## Pipeline

1. Start from the permitted VISCERAL and IRCAD CT data and associated segmentations.
2. Generate physics-based ultrasound images through ray casting.
3. Convert the generated images and corresponding semantic maps to the polar-coordinate representation used by the models.
4. Encode the semantic labels with a stable class-to-channel mapping.
5. Normalize image intensities using one documented convention shared by training, inference, and evaluation.
6. Resize inputs and targets to the model resolution.
7. Export paired samples while preserving the source `patient_id` and `volume_id`.

## Information required for exact reproduction

Complete the following fields from the preprocessing implementation or experiment logs before publication:

- `TODO`: CT intensity clipping/windowing and voxel resampling.
- `TODO`: segmentation post-processing and class definitions.
- `TODO`: ray-casting geometry, probe parameters, physical simulation settings, and randomization.
- `TODO`: polar-coordinate origin, angular/radial ranges, and interpolation method.
- `TODO`: label-to-channel mapping and treatment of background/unknown labels.
- `TODO`: intensity normalization range and whether statistics are global or split-specific.
- `TODO`: output image resolution and interpolation kernels.
- `TODO`: training-time augmentation.

## Leakage prevention

Preprocessing must preserve the patient/volume split. Any operation that estimates statistics, fits a transform, or generates paired samples must be applied without allowing test-volume information to influence training or model selection. The `train/`, `validation/`, and `test/` directories distributed with the Kaggle dataset are the authoritative partition record, and the patient identifier in each filename must remain associated with its original split.

## File naming

Use a stable shared stem for paired artifacts, for example:

```text
<volume_id>_<sample_id>_label.<ext>
<volume_id>_<sample_id>_physics.<ext>
<volume_id>_<sample_id>_target.<ext>
```

The exact extension and sample identifier may vary, but labels, physics-based references, targets, and generated outputs should be traceable to the same volume and sample without relying on directory order.
