# Evaluation protocol

This document describes the evaluation code retained for the ABME26 experiments. It distinguishes paired Stage I evaluation against physics-based targets from unpaired Stage II evaluation against real ultrasound images. 

## Stage I paired metrics

MAE, MSE, PSNR, SSIM, and LPIPS are computed independently for every paired test image with TorchMetrics. The reported summary is the arithmetic mean and sample standard deviation across the 245 images.

| Metric | Retained implementation | Input and parameters | Direction |
| --- | --- | --- | --- |
| MAE | `torchmetrics.functional.regression.mean_absolute_error` | RGB float tensor retaining the `[0, 255]` values; mean over channels and pixels | Lower is better |
| MSE | `torchmetrics.functional.regression.mean_squared_error` | RGB float tensor retaining the `[0, 255]` values; mean over channels and pixels | Lower is better |
| PSNR | `torchmetrics.image.PeakSignalNoiseRatio` | RGB `[0, 255]`; `data_range=255.0` | Higher is better |
| SSIM | `torchmetrics.functional.image.structural_similarity_index_measure` | RGB `[0, 1]`; `data_range=1.0`; all window/kernel constants left at TorchMetrics defaults | Higher is better |
| LPIPS | `torchmetrics.image.lpip.LearnedPerceptualImagePatchSimilarity` | RGB `[0, 1]`; SqueezeNet backbone (`net_type='squeeze'`); `normalize=True` | Lower is better |

### Stage I semantic preservation

The mIoU implementation is custom PyTorch code. RGB masks are decoded by exact palette matching into the 16 classes documented under [`data/test_labels/`](../data/test_labels/README.md). Unmatched RGB values become background (`0`).

For each image and each class `0`--`15`, IoU is computed as intersection divided by union. A class is omitted from that image's mean only when its union is zero in both prediction and ground truth. Background is included. The final result is the mean and sample standard deviation of the per-image mIoUs.

The predicted masks are precomputed segmentations of the generated US images. We used Segmenter S, the same segmentation model employed in SG-CycleGAN. Further details are provided in configs/sgcyclegan.md.


### Stage I FID and KID

We uses TorchMetrics directly:
   - `FrechetInceptionDistance(feature=768)`;
   - `KernelInceptionDistance(feature=768, subset_size=80)`;
   - RGB `uint8` inputs in `[0, 255]`;
   - PyTorch seed `123`;

## Stage II FID and KID

Stage II FID/KID compare SG-CycleGAN-refined outputs against real polar ultrasound images. The retained method is a custom implementation using a `torch-fidelity` Inception-v3 feature extractor copied from the TorchMetrics `v1.2.1` implementation.

### Feature extraction

- Input is RGB `uint8` in `[0, 255]`.
- Images are resized internally to `299 x 299` using the TensorFlow-1-compatible bilinear interpolation supplied by `torch-fidelity`, with `align_corners=False`.
- Intensities are normalized as `(x - 128) / 128`.
- The extractor is `inception-v3-compat` with pretrained `torch-fidelity` weights.
- Features are taken after `Mixed_6e`, adaptively average-pooled to a 768-dimensional vector.
- The network is forced into evaluation mode.

This is deliberately not the conventional 2,048-dimensional final-pool FID. Scores are only comparable with evaluations using the same 768-dimensional layer and preprocessing.

## Stage II organ-level echogenicity

The organ analysis uses Pillow, NumPy, SciPy, and scikit-image plus a custom chi-squared distance implementation.

Verified processing steps are:

1. Decode RGB labels by exact color matching.
2. Select the organ mask.
3. Erode the binary mask with a `5 x 5` all-ones structuring element using `scipy.ndimage.morphology.binary_erosion`.
4. Extract image values inside the eroded mask.
5. Create a 50-bin NumPy histogram over the fixed range `[0, 255]` with `density=False`, then divide by its total so each image histogram sums to one.
6. Keep real patches whose pre-erosion organ area is at least the minimum real-organ area. Keep generated patches whose area is at least the mean real-organ area.
7. Compute all real--real and all real--generated histogram pairs; the real--real distribution includes self-comparisons.

The custom distance is:

```text
d(A, B) = 0.5 * sum((A_i - B_i)^2 / (A_i + B_i))
```

