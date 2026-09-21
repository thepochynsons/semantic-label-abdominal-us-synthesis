# Data organization

This repository does not redistribute the original CT datasets or any derivative that is restricted by their terms of use. Obtain the source data from the VISCERAL Anatomy3 Challenge and IRCAD, and obtain the processed dataset from [Kaggle](https://www.kaggle.com/datasets/ignaciorlando/ussimandsegm).

The local data root used by an implementation should contain, at minimum:

```text
data_root/
├── labels/                 # semantic label maps
├── physics_based_images/   # ray-cast ultrasound images
├── ultrasound/             # real or target ultrasound images, if applicable
└── metadata/               # optional volume and acquisition metadata
```

The exact directory names may be adapted by an implementation. The split manifest is the source of truth for membership in train, validation, and test partitions. Every row must identify the patient or source volume, and all 2D samples from that volume must use the same `split` value.

The repository-level example directories `test_labels/` and `test_references/` are reserved for small, redistributable representative samples. They are not a substitute for the full processed dataset.
