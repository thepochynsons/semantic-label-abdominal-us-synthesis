# Representative model outputs

This directory contains representative synthetic US images for the two test samples distributed under [`data/test_labels/`](../data/test_labels/) and their corresponding physics-based references under [`data/test_references/`](../data/test_references/).

The images illustrate both stages of the reported pipeline:

- **Stage I — semantic image synthesis (SIS):** Pix2Pix and the Semantic Diffusion Model (SDM) synthesize ultrasound images directly from semantic label maps.
- **Stage II — appearance refinement:** SG-CycleGAN translates the Stage I output toward the real-US appearance domain.

All files in this directory are model outputs. The examples are qualitative artifacts only and must not be treated as the complete evaluation set.

## Stage I SIS outputs

### Pix2Pix

[`pix2pix/`](pix2pix/) contains one deterministic Pix2Pix synthesis for each representative semantic map.

### Semantic Diffusion Model

[`sdm/`](sdm/) contains outputs generated from the same conditioning label at four denoising-step settings: `250`, `500`, `750`, and `1000`. The examples expose the effect of the inference budget while keeping the sample identity fixed.

## Stage II SG-CycleGAN outputs

[`SG-CycleGAN/`](SG-CycleGAN/) contains the corresponding Stage II refinement results. The filename prefix identifies the Stage I source provided as input to the shared SG-CycleGAN refinement model. For SDM, the model with 1000 denoising steps was used.