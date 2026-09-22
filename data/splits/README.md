# Dataset splits

The processed dataset used in the experiments is available from [Kaggle](https://www.kaggle.com/datasets/ignaciorlando/ussimandsegm). The Kaggle release contains separate directories for the `train`, `validation`, and `test` partitions.

The partition is performed at **patient level**. All images derived from the same patient are assigned to the same split.

## Split organization

| Directory | Use |
| --- | --- |
| `train/` | Model training |
| `validation/` | Model selection and hyperparameter decisions |
| `test/` | Final evaluation |

The split directories in the Kaggle release are the authoritative source of the partition.

## Filename convention

Each image filename contains a patient identifier and an indication of the source dataset:

- Filenames whose identifier starts with `CT` correspond to volumes from the [VISCERAL Anatomy3 dataset](https://visceral.eu/closed-benchmarks/anatomy3/).
- Filenames whose identifier contains `ircad` correspond to volumes from the [IRCAD 3D-IRCADb-01 dataset](https://www.ircad.fr/research-and-development/data-sets/liver-segmentation-3d-ircadb-01/).

The patient identifier is part of the filename and should be preserved when pairing semantic labels, physics-based images, targets, and generated outputs.

An additional CSV manifest is not required because the split is already distributed by patient-level directories in the Kaggle dataset.
