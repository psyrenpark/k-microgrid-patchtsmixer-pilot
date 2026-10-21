# k-microgrid-patchtsmixer-pilot

An experimental microgrid regression model with a reproducible CPU inference package on Hugging Face.

This repository contains the project overview, license, and attribution notices. The inference code, configuration, synthetic example, evaluation results, and weights are available together on [Hugging Face](https://huggingface.co/psyrenpark/k-microgrid-patchtsmixer-pilot).

Author: [Psyren Park](https://huggingface.co/psyrenpark)

## Model at a glance

| Item | Details |
| --- | --- |
| Architecture | Transformers PatchTSMixer backbone with a custom regression head |
| Task | 15-slot microgrid power-target regression |
| Input | 4 chronological observations, 290 features each, 15-minute interval |
| Output | 65 targets, including predictions in physical units |
| Input contract | `gh_15slot_15min_v1` |
| Training | Fresh initialization, 1 epoch, 40 scenarios: 28 train / 6 validation / 6 test |
| Inference | CPU, FP32, single thread |

## Download the inference package

Use the following fixed Hugging Face revision for the package described here:

```text
bea930d8ce21b02ed48cc29b5097cf276e4000ff
```

Open the [files at this revision](https://huggingface.co/psyrenpark/k-microgrid-patchtsmixer-pilot/tree/bea930d8ce21b02ed48cc29b5097cf276e4000ff) and download all 12 files listed below into one local folder named `k-microgrid-patchtsmixer-pilot`. Use the file download buttons to obtain the actual files, including the full `model.safetensors` weights.

```text
LICENSE
NOTICE
README.md
config.json
example_input.json
infer.py
manifest.json
metrics.csv
model.py
model.safetensors
provenance.json
requirements.txt
```

Keep the downloaded Hugging Face README, NOTICE, and manifest together with that package. The README and NOTICE in this documentation repository are separate documents; copying them over the downloaded files would invalidate the corresponding package-manifest hashes. Cloning this documentation repository alone does not provide the inference package.

The [fixed-revision model card](https://huggingface.co/psyrenpark/k-microgrid-patchtsmixer-pilot/blob/bea930d8ce21b02ed48cc29b5097cf276e4000ff/README.md) contains the complete input/output contract and package instructions.

## Run CPU inference

Use Python 3.11 with the dependencies in the package's [requirements.txt](https://huggingface.co/psyrenpark/k-microgrid-patchtsmixer-pilot/blob/bea930d8ce21b02ed48cc29b5097cf276e4000ff/requirements.txt) already available. The recorded CPU validation environment used Torch 2.11.0+cpu, NumPy 1.26.4, Transformers 4.40.2, and Safetensors 0.8.0.

From the folder containing the downloaded `infer.py`, run:

```console
python infer.py --input example_input.json
```

The command prints a JSON object with `contract_id`, `target_order`, `units`, `network`, `normalized`, and `physical`. There are 65 predictions in each output space. Inference loads local files without downloading another model.

Inputs must have the exact feature order and a finite `input[4][290]` array in the documented capacity-normalized feature space. Raw equipment values require the corresponding feature transformation. Units, scales, preprocessing, and target order are defined in the package's [config.json](https://huggingface.co/psyrenpark/k-microgrid-patchtsmixer-pilot/blob/bea930d8ce21b02ed48cc29b5097cf276e4000ff/config.json). The example is synthetic and has no real-world ground truth.

## Evaluation and limitations

Results below use 6 held-out test scenarios and 576 test samples. The baseline predicts the training-set mean.

| Metric | Value |
| --- | ---: |
| Model normalized MAE | 0.298604 |
| Model normalized RMSE | 0.375961 |
| Mean-prediction baseline normalized MAE | 0.239194 |

The model has a higher MAE than the baseline. It is a small experimental result; no improvement in predictive performance, generalization, or real-world effectiveness is claimed. Per-target physical-unit MAE/RMSE and baseline results are in [metrics.csv](https://huggingface.co/psyrenpark/k-microgrid-patchtsmixer-pilot/blob/bea930d8ce21b02ed48cc29b5097cf276e4000ff/metrics.csv).

CPU FP32 outputs matched the original checkpoint for three synthetic inputs (zeros, signed ramp, and clipping), with maximum absolute error 0 in all three output spaces at `rtol=1e-6`, `atol=1e-7`. The published package was downloaded and checked with the existing CPU environment. The fixed revision above changes only the model card and its manifest hash from that CPU-validated package; the other ten files are byte-identical.

Output parity verifies packaging and inference behavior. It does not establish accuracy or safety on real equipment. Do not use this model for operational control decisions. Training data, optimizer state, and a full training pipeline are not distributed here.

## License and attribution

Copyright 2026 Psyren Park.

This repository's documentation is licensed under Apache-2.0; see [LICENSE](LICENSE). The linked Hugging Face code and model weights are also licensed under Apache-2.0. See [NOTICE](NOTICE) for the scope of the reproduced package attribution notices. Upstream authors retain their copyrights and license terms; no endorsement is claimed.
