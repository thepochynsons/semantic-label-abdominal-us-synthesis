# Semantic Label Abdominal US Synthesis

Reproducibility resources for the paper:

**“Abdominal Ultrasound Simulation from Semantic Labels Using Paired Label-to-Physics-Based Image Translation”**

This repository provides supplementary materials to facilitate reproduction and evaluation of the experiments reported in the paper. It includes information about data organization and preprocessing, representative semantic inputs, test samples, generated outputs from Pix2Pix and the Semantic Diffusion Model (SDM), and instructions for reproducing the experimental and evaluation procedures.

## Paper

**Title:** Abdominal Ultrasound Simulation from Semantic Labels Using Paired Label-to-Physics-Based Image Translation

**Authors:** Santiago Vitale, Duilio Esteban Deangeli, Ignacio Larrabide, and José Ignacio Orlando

**Journal:** Annals of Biomedical Engineering

**Status:** Under review / Accepted / Published

**DOI:** To be added after publication.

---

## Repository Structure

```text
semantic-label-abdominal-us-synthesis/
│
├── README.md
├── data/
│   ├── test_labels/
│   ├── test_references/
│   └── splits/
│
├── outputs/
│   ├── pix2pix/
│   └── sdm/
│
├── configs/
│   ├── pix2pix.md
│   ├── sdm.md
│   └── sgcyclegan.md
│
├── evaluation/
│   └── README.md
│
└── docs/
    ├── preprocessing.md
    ├── training.md
    └── inference.md
