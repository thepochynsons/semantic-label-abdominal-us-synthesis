# Data organization

This repository does not redistribute the original CT datasets or any derivative that is restricted by their terms of use. Obtain the source data from [VISCERAL Anatomy3](https://visceral.eu/closed-benchmarks/anatomy3/) and the [IRCAD 3D-IRCADb-01 liver segmentation dataset](https://www.ircad.fr/research-and-development/data-sets/liver-segmentation-3d-ircadb-01/), and obtain the processed dataset from [Kaggle](https://www.kaggle.com/datasets/ignaciorlando/ussimandsegm).

The local data root used by an implementation should contain, at minimum:

```text
data_root/
├── labels/                 # semantic label maps
├── physics_based_images/   # ray-cast ultrasound images
├── ultrasound/             # real or target ultrasound images, if applicable
└── metadata/               # optional volume and acquisition metadata
```

The exact directory names may be adapted by an implementation. For the released experiment data, the split directories included in the Kaggle download are the source of truth for membership in train, validation, and test partitions. If a patient/volume manifest is available, use it to verify that all 2D samples from one volume remain in the same split.

The repository-level example directories `test_labels/` and `test_references/` are reserved for small, redistributable representative samples. They are not a substitute for the full processed dataset.
