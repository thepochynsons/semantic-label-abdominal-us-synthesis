# Representative semantic labels

This directory contains representative semantic label maps from the test split of the processed [US simulation & segmentation dataset](https://www.kaggle.com/datasets/ignaciorlando/ussimandsegm).

The examples include one sample from each source dataset:

| File | Source dataset | Description |
| --- | --- | --- |
| `resized_ct20-29.png` | VISCERAL Anatomy3 | Representative abdominal CT-derived semantic label map. |
| `resized_ircad17-3.png` | IRCAD 3D-IRCADb-01 | Representative abdominal label map containing a tumor, included as a pathological example. |

The filenames preserve the patient identifier and source-dataset convention described in [data/splits/README.md](../splits/README.md). 

## Color and organ labels

The semantic color mapping follows the label description published with the Kaggle dataset:

| Color | Organ or anatomical structure |
| --- | --- |
| Violet | Liver |
| Yellow | Kidney |
| Blue | Pancreas |
| Red | Vessels |
| Light blue | Adrenals |
| Green | Gallbladder |
| White | Bones |
| Pink | Spleen |


