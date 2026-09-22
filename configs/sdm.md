# Semantic Diffusion Model configuration

> **Implementation repository.** The SDM implementation used for these experiments was adapted from
> [david-stojanovski/echo_from_noise](https://github.com/david-stojanovski/echo_from_noise).

The Semantic Diffusion Model (SDM) is the diffusion-based Stage I model. A semantic map conditions an ADM-style U-Net through SPADE-like group-normalization layers.

## Experiment configuration

| Component | Setting |
| --- | --- |
| Image size | `128 x 128` pixels |
| Image channels | 3 |
| Semantic classes | 16, supplied as a one-hot tensor |
| Base U-Net width | 64 channels |
| Channel multipliers | `(1, 1, 2, 3, 4)` |
| Residual blocks per level | 2 |
| Attention resolutions | `32`, `16`, and `8` pixels |
| Attention | 64 channels per head |
| Resampling | Residual blocks for upsampling and downsampling |
| Dropout | `0.0` |
| Scale-shift timestep normalization | Enabled |
| Semantic normalization | SPADE group normalization with 32 groups and a 128-channel shared conditioning MLP |
| Training diffusion horizon | 1000 steps in the archived run record |
| Noise schedule | Cosine, `alpha_bar(t) = cos^2(((t + 0.008) / 1.008) * pi/2)`, with `beta <= 0.999` |
| Prediction target | Noise (`epsilon`) |
| Variance | Learned range; the denoiser emits 6 channels (3 noise + 3 variance) |
| Main loss | Mean squared error on predicted noise |
| Variational-bound term | Enabled through learned variance and weighted by `0.001` |
| Direct KL objective | Disabled (`use_kl=False`) |
| Timestep sampler | Uniform |
| Optimizer | AdamW |
| Learning rate | `1e-4` |
| AdamW parameters | PyTorch defaults `betas=(0.9, 0.999)`, `eps=1e-8`; weight decay `0.0` |
| LR schedule | Linear decay toward zero over `5,000,000` optimization steps |
| Batch size | 12 per distributed process |
| Microbatch | Disabled (`-1`; the full local batch is used) |
| EMA | `0.9999`, updated after each successful optimizer step with no warm-up |
| Mixed precision | FP16 enabled; initial log loss scale `20.0`, scale-growth factor `0.001` |
| Checkpoint interval | 10,000 optimization steps |

The value `0.001` has two distinct uses in the implementation: it weights the variational-bound term used to learn the variance, and it is also the FP16 loss-scale growth factor. It should not be described simply as a standalone "KL loss weight." Because `use_kl=False`, training uses the MSE path with the additional learned-variance variational term rather than the direct rescaled-KL objective.

## Semantic conditioning

Class-index masks are converted into 16-channel one-hot tensors. No instance-edge channel is appended (`no_instance=True`). SPADE conditioning is applied in both middle-block residual units and throughout the decoder/output residual blocks; the encoder residual blocks use the standard ADM normalization. Each SPADE layer resizes the semantic tensor to the current feature resolution using nearest-neighbor interpolation.

## Data transformation and augmentation

- RGB images are resized to `128 x 128` with bicubic interpolation and normalized to `[-1, 1]`.
- Semantic maps are resized to the same dimensions with nearest-neighbor interpolation.
- Random cropping is disabled.
- Paired horizontal flipping is enabled with probability `0.5` during training.
- The training loader shuffles samples and drops the last incomplete batch.


