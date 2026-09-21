# Dataset split manifests

Splits are defined at patient/volume level. Do not split individual 2D slices independently: all slices, label maps, physics-based images, and paired targets derived from the same CT volume must remain in one partition.

The recommended manifest is a UTF-8 CSV with one row per source volume:

```text
patient_id,volume_id,source_dataset,split,number_of_2d_samples,notes
```

Field definitions:

- `patient_id`: anonymized patient identifier, stable across all derived files.
- `volume_id`: source CT volume identifier.
- `source_dataset`: `visceral`, `ircad`, or another documented source.
- `split`: exactly one of `train`, `validation`, or `test`.
- `number_of_2d_samples`: number of exported 2D samples derived from this volume.
- `notes`: optional provenance or exclusion note; do not include protected personal information.

The final paper manifest should be added to this directory as `splits.csv` or another clearly named versioned file. Before release, verify that:

1. each `volume_id` occurs exactly once;
2. no patient or volume occurs in more than one split;
3. the counts agree with the exported data;
4. the test set is not used for model selection.

`split_template.csv` is an empty schema template and does not contain the actual experiment partitions.
