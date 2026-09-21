# Pix2Pix configuration

Pix2Pix is the GAN-based Stage I baseline used to translate semantic label maps into physics-based ultrasound images.

## Experiment configuration

| Component | Setting |
| --- | --- |
| Input | One-channel class-index map with values `0`--`15` |
| Output | Three-channel image |
| Spatial resolution | `256 x 256` pixels |
| Generator | Seven-level U-Net (`ngf=64`) with skip connections |
| Generator normalization | Batch normalization |
| Generator upsampling | Nearest-neighbor `2x` upsampling, reflection padding, and `3 x 3` convolution |
| Generator dropout | Disabled |
| Generator output | `tanh` |
| Discriminator | Conditional PatchGAN-like discriminator; four `4 x 4`, stride-2 blocks with `64, 128, 256, 512` channels and a final `1 x 1` convolution |
| Adversarial objective | Binary cross-entropy with logits |
| Reconstruction objective | L1 loss |
| L1 weight | `100` |
| Optimizers | Separate Adam optimizers for generator and discriminator |
| Learning rate | `2e-4` for both optimizers |
| Adam parameters | `betas=(0.9, 0.999)`, `eps=1e-8`, no weight decay, `amsgrad=False` (PyTorch defaults used by the training code) |
| Batch size | `64` |
| Update schedule | One discriminator update followed by one generator update per minibatch |
| Weight initialization | Convolution weights sampled from `N(0, 0.02)`; batch-normalization weights sampled from `N(0, 0.02)` and biases set to zero |
| Random seed | `2023` for Python `random`, NumPy, PyTorch CPU, and the current CUDA device |
| Training horizon | `max_epochs=2001` (epoch indices `0`--`2000`) |
| Checkpoint cadence | Every 5 epochs; all periodic 

The generator consumes the class-index mask directly after conversion to `float`; the mask is neither one-hot encoded nor normalized. The target RGB image is normalized channel-wise with mean `0.5` and standard deviation `0.5`, producing values approximately in `[-1, 1]`. At inference, the `tanh` output is mapped back to `[0, 255]`.

## Data transformation and augmentation

- Training pairs are matched by filename.
- A paired horizontal flip is applied with probability `0.5`.
- No resize is active in the final `256 x 256` training pipeline; inputs must already have the expected dimensions.
- No other crop, geometric transform, or intensity augmentation is configured.
- Training samples are shuffled. The loader uses `drop_last=False` and, because no worker count is passed, PyTorch's default `num_workers=0`.
- Inference applies only the RGB-to-class-index conversion and tensor conversion to the semantic map.

The exact RGB-to-class mapping must remain identical across data preparation and inference. It contains 16 values including background (`0`) and 15 anatomical/pathological classes (`1`--`15`). The canonical mapping should live in `docs/preprocessing.md` rather than be duplicated here.
