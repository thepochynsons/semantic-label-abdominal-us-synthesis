# SG-CycleGAN configuration

> **Implementation repository.** The experiments used
> [thepochynsons/pytorch-CycleGAN-and-pix2pix](https://github.com/thepochynsons/pytorch-CycleGAN-and-pix2pix),
> a research fork of
> [junyanz/pytorch-CycleGAN-and-pix2pix](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix).

SG-CycleGAN is the Stage II refinement model. It learns an unpaired translation from artificial/simulated ultrasound (AUS) to real ultrasound (RUS), while a fixed semantic segmenter constrains the AUS cycle reconstruction.
The same trained AUS-to-RUS generator is then applied to cached outputs from each Stage I method.

## Experiment configuration

| Component | Setting |
| --- | --- |
| Source domain | Artificial/simulated US (AUS) |
| Target domain | Real US (RUS) |
| Generators | Two 8-level U-Nets base width 64 |
| Generator upsampling | Nearest-neighbor `2x` upsampling, reflection padding, and `3 x 3` convolution |
| Generator normalization | Instance normalization |
| Generator dropout | Disabled |
| Discriminators | Two 70 x 70 PatchGAN, base width 64, 3 layers |
| GAN objective | Least-squares GAN (LSGAN) |
| AUS cycle weight | `lambda_aus=10.0` |
| RUS cycle weight | `lambda_rus=10.0` |
| Identity multiplier | `lambda_identity=0.5` |
| Semantic loss | Cross-entropy on the reconstructed AUS segmentation |
| Semantic loss weight | `lambda_S=0.5` |
| Optimizers | Adam for the two generators jointly and Adam for the two discriminators jointly |
| Learning rate | `2e-4` |
| Adam parameters | `betas=(0.5, 0.999)` |
| Batch size | 2 |
| Image pools | 50 previously generated images per domain |
| Training duration | 100 epochs: 50 at the initial rate followed by 50 with linear decay |
| Weight initialization | Normal distribution with mean 0 and standard deviation `0.02`; biases zero |
| Checkpoint cadence | Every epoch |
| Checkpoint used for reported inference | Epoch `80` (`80_net_G_aus.pth`) |

## Semantic segmenter

The semantic guidance network is a pretrained segmenter bundled with the SG-CycleGAN implementation. 

| Component | Segmenter setting |
| --- | --- |
| Architecture | 2D U-Net with four encoder and four decoder levels |
| Input | One-channel grayscale AUS, `256 x 256` pixels |
| Encoder widths | `64, 128, 256, 512, 1024` |
| Decoder widths | `512, 256, 128, 64` |
| Blocks | Two `3 x 3` convolutions per block, each followed by batch normalization and ReLU |
| Down/up-sampling | `2 x 2` max pooling and nearest-neighbor `2x` upsampling |
| Output | `1 x 1` convolution producing 16 per-pixel logits |
| Dropout | Disabled |
| Classes | Background plus 15 anatomical/pathological classes; see the canonical [label table](../data/test_labels/README.md#canonical-class-and-color-mapping) |
| Training loss | Unweighted 16-class cross-entropy |
| Optimizer | Adam, learning rate `1e-4`, `betas=(0.9, 0.999)`, weight decay `0` |
| Batch size | `32` |
| Input normalization | Grayscale tensor normalized with mean `0.5` and standard deviation `0.5` |
| Augmentation | Paired horizontal flip of image and label with probability `0.5` |
| LR scheduler | `ReduceLROnPlateau`, maximizing validation Dice; factor `0.5`, patience `20` epochs |
| Configured/traced training length | `1000` epochs configured |

### Use in the SG-CycleGAN objective

During SG-CycleGAN training, the segmenter is set to evaluation mode and omitted from both SG-CycleGAN optimizers, so its parameters are not updated. For an AUS sample `x`, the semantic target is a pseudo-label map produced by the same network:

```text
y_hat = argmax(S(x), dim=class)
L_sem = CrossEntropy(S(G_rus(G_aus(x))), y_hat)
```

Thus, the semantic term enforces agreement between the original simulated image and its AUS cycle reconstruction; it is not supervised directly by a ground-truth label map during SG-CycleGAN training. 

## Preprocessing and augmentation

- Independent random horizontal flips are enabled for both unpaired domains during training.
- Images are converted to grayscale and normalized with mean `0.5` and standard deviation `0.5`.
- Inputs are expected at `256 x 256`; the recorded SG-CycleGAN run uses `preprocess=none`, so training does not resize or crop them.
