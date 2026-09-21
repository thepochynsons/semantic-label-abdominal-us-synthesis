# SG-CycleGAN configuration

SG-CycleGAN is the Stage II refinement model. It learns an unpaired translation from artificial/simulated ultrasound (AUS) to real ultrasound (RUS), while a fixed semantic segmenter constrains the AUS cycle reconstruction. 
The same trained AUS-to-RUS generator is then applied to cached outputs from each Stage I method.

## Experiment configuration

| Component | Setting |
| --- | --- |
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


The semantic segmenter is a pretrained 16-class 2D U-Net. During SG-CycleGAN training it is placed in evaluation mode and excluded from the SG-CycleGAN optimizers. Pseudo-labels are obtained by applying the segmenter to the original AUS image and taking the per-pixel `argmax`. Cross-entropy then compares those pseudo-labels with the segmenter's logits for the cycle reconstruction `G_rus(G_aus(AUS))`.

## Preprocessing and augmentation

- Independent random horizontal flips are enabled for both unpaired domains during training.
- Images are converted to grayscale and normalized with mean `0.5` and standard deviation `0.5`.
