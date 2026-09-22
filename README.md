# Abdominal Ultrasound Simulation from Semantic Labels Using Paired Label-to-Physics-Based Image Translation

Reproducibility resources for the paper by Santiago Vitale, Duilio Esteban Deangeli, Ignacio Larrabide, and Jose Ignacio Orlando.

**Journal:** Annals of Biomedical Engineering<br>
**Status:** Under review<br>
**Code:** The complete source code is not distributed because of project-related restrictions.

This repository contains the data description, the organization of the official dataset splits, model configurations, representative inputs and outputs, and evaluation instructions needed to inspect and reproduce the main experimental procedures reported in the paper.

## Data

The original CT datasets used in this study are publicly available from:

- [VISCERAL Anatomy3](https://visceral.eu/closed-benchmarks/anatomy3/)
- [IRCAD 3D-IRCADb-01 liver segmentation dataset](https://www.ircad.fr/research-and-development/data-sets/liver-segmentation-3d-ircadb-01/)

The processed dataset used in the paper is available from [Kaggle](https://www.kaggle.com/datasets/ignaciorlando/ussimandsegm). This repository does not redistribute data subject to the terms of use of the original datasets.

Download the processed data separately and keep it outside this repository unless its license permits redistribution. The expected organization and naming conventions are described in [data/README.md](data/README.md).

### Dataset splits

Experiments use train, validation, and test partitions distributed with the processed Kaggle dataset. The split is performed at patient level and is documented in [data/splits/README.md](data/splits/README.md).

## Reproduction resources

```text
.
├── README.md
├── configs/
│   ├── pix2pix.md
│   ├── sdm.md
│   └── sgcyclegan.md
├── data/
│   ├── README.md
│   ├── splits/
│   │   ├── README.md
│   │   └── README.md
│   ├── test_labels/
│   └── test_references/
├── docs/
│   └── preprocessing.md
├── evaluation/
│   └── README.md
└── outputs/
    ├── pix2pix/
    └── sdm/
```

The `data/test_labels/` and `data/test_references/` directories are intended for small, redistributable representative samples and physics-based reference images. Generated examples are grouped by model under `outputs/`.

## Stage I models

Two semantic-label-to-ultrasound models were evaluated:

- **Pix2Pix:** U-Net generator, PatchGAN discriminator, adversarial plus L1 reconstruction loss, Adam, batch size 64, learning rate `2e-4`, L1 weight 100, and 2000 training epochs. See [configs/pix2pix.md](configs/pix2pix.md).
- **Semantic Diffusion Model (SDM):** ADM-style U-Net with SPADE semantic conditioning, cosine noise schedule, Adam, EMA, mixed precision, batch size 12, learning rate `1e-4`, and KL weight `0.001`. Inference was evaluated with multiple denoising-step counts. See [configs/sdm.md](configs/sdm.md).

## Stage II refinement

Stage II uses the SG-CycleGAN refinement strategy introduced in earlier work. It was retrained on the datasets described in the paper and applied as a downstream module to assess compatibility with the refinement strategy. See [configs/sgcyclegan.md](configs/sgcyclegan.md).

## Preprocessing and evaluation

The preprocessing pipeline includes CT segmentation, physics-based ultrasound generation through ray casting, polar conversion, semantic label encoding, intensity normalization, and image resizing. See [docs/preprocessing.md](docs/preprocessing.md).

The evaluation protocol covers MAE, MSE, PSNR, SSIM, mIoU, LPIPS, FID, KID, and Stage II organ-level echogenicity analysis using histogram-based chi-squared distances. See [evaluation/README.md](evaluation/README.md).

## Hardware and software

The reported experiments were performed with Ubuntu 18.04.6 LTS, an Intel Core i7-10700K CPU, 16 GB RAM, an NVIDIA Quadro P6000 with 24 GB VRAM, and PyTorch 2.0.1. Additional dependency versions and environment details should be added as they are finalized.

## Reproducibility status

Files currently included in this repository are documentation and reproducibility artifacts. Values marked `TODO` identify details that should be filled from the experiment logs before publication. Representative images and the final Kaggle version/count record should be added without committing the original restricted CT datasets.

Additional implementation details may be provided by the corresponding author upon reasonable request.

## Citation

```bibtex
@article{vitale2026abdominal,
  title   = {Abdominal Ultrasound Simulation from Semantic Labels Using Paired Label-to-Physics-Based Image Translation},
  author  = {Vitale, Santiago and Deangeli, Duilio Esteban and Larrabide, Ignacio and Orlando, Jose Ignacio},
  journal = {Annals of Biomedical Engineering},
  year    = {2026},
  note    = {Under review}
}
```

For questions about the repository or additional implementation details, contact the corresponding author.
