# Representative semantic labels

This directory contains representative semantic label maps from the test split of the processed [US simulation & segmentation dataset](https://www.kaggle.com/datasets/ignaciorlando/ussimandsegm).

The examples include one sample from each source dataset:

| File | Source dataset | Description |
| --- | --- | --- |
| `resized_ct20-29.png` | VISCERAL Anatomy3 | Representative abdominal CT-derived semantic label map. |
| `resized_ircad17-3.png` | IRCAD 3D-IRCADb-01 | Representative abdominal label map containing a tumor, included as a pathological example. |

Each filename matches its corresponding file under `data/test_references/`. The patient identifier and source-dataset convention are described in [the split documentation](../splits/README.md).

## Canonical class and color mapping

The model input is a categorical mask, not a continuous-color image. Use lossless storage and nearest-neighbor interpolation for class maps. 

RGB values are listed in channel order `(R, G, B)`. Class `0` is both the explicit background class and the fallback value used by the retained exact-color decoder when no foreground color matches.

| Class ID | Name | RGB | Hex | Visual description |
| ---: | --- | --- | --- | --- |
| 0 | Background / unlabeled | `(0, 0, 0)` | `#000000` | Black |
| 1 | Bone | `(255, 255, 255)` | `#FFFFFF` | White |
| 2 | Artery | `(255, 0, 0)` | `#FF0000` | Red |
| 3 | Pancreas | `(0, 0, 255)` | `#0000FF` | Blue |
| 4 | Gallbladder | `(0, 255, 0)` | `#00FF00` | Green |
| 5 | Kidney | `(255, 255, 0)` | `#FFFF00` | Yellow |
| 6 | Adrenals | `(0, 255, 255)` | `#00FFFF` | Cyan |
| 7 | Liver | `(100, 0, 100)` | `#640064` | Purple |
| 8 | Spleen | `(255, 0, 255)` | `#FF00FF` | Magenta |
| 9 | Vein | `(169, 50, 38)` | `#A93226` | Dark red |
| 10 | Colon | `(27, 94, 32)` | `#1B5E20` | Dark green |
| 11 | Duodenum | `(239, 108, 0)` | `#EF6C00` | Orange |
| 12 | Small bowel | `(121, 85, 72)` | `#795548` | Brown |
| 13 | Stomach | `(38, 166, 154)` | `#26A69A` | Teal |
| 14 | Torso fat | `(151, 151, 147)` | `#979793` | Gray |
| 15 | Tumor | `(187, 143, 206)` | `#BB8FCE` | Light purple |




