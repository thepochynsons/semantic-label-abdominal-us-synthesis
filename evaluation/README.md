# Evaluation protocol

This directory documents how to evaluate the representative or locally regenerated outputs. The implementation used for the paper is not distributed; use equivalent, version-pinned implementations and record the package versions and options used.

## Inputs

For every evaluated sample, identify:

- semantic label map;
- physics-based reference image;
- generated Stage I image;
- target image, if the metric requires one;
- source `patient_id`, `volume_id`, and split.

Only the test partition should be used for final reported scores. Preserve the same spatial resolution, intensity range, masking convention, and organ labels across all methods being compared.

## Metrics

### Morphological and anatomical metrics

- **MAE:** mean absolute pixel-wise error.
- **MSE:** mean squared pixel-wise error.
- **PSNR:** peak signal-to-noise ratio, with the declared data range.
- **SSIM:** structural similarity with explicitly recorded window, constants, and data range.
- **mIoU:** mean intersection over union for semantic/anatomical masks, with the class list and absent-class policy documented.

### Perceptual and distributional metrics

- **LPIPS:** record the backbone and input normalization.
- **FID:** record feature extractor, image preprocessing, sample count, and whether statistics are computed per split or from a fixed reference set.
- **KID:** record feature extractor, subset size, number of subsets, and estimator settings.

### Stage II organ-level echogenicity

Compute organ-level intensity histograms for the defined anatomical regions and compare generated/refinement outputs using histogram-based chi-squared distances. Record the bin edges, normalization, masking rule, zero-bin handling, organ list, and aggregation rule.

## Reproducible evaluation record

For each run, save a manifest containing:

```text
method,stage,checkpoint,split,denoising_steps,metric,organ,score
```

Also record the evaluator version, random seed where applicable, number of samples, excluded samples and reasons, and confidence interval or aggregation procedure.

## Pending exact details

The following paper-specific values should be filled before publication:

- `TODO`: exact test-set sample count.
- `TODO`: metric package/library versions.
- `TODO`: LPIPS/FID/KID feature extractors and preprocessing.
- `TODO`: SSIM window and data range.
- `TODO`: mIoU class mapping and absent-class handling.
- `TODO`: histogram bins and chi-squared implementation.
