
## Repository structure

```text
main/
├── models/
│   ├── completion.py
│   ├── clustering.py
│   ├── fusion.py
│   ├── losses.py
│   └── network.py
├── tasks/
│   ├── run_completion.py
│   ├── run_clustering.py
│   ├── evaluate_clustering.py
│   └── build_consensus.py
├── data/feature_type.json
├── configs.py
├── utils.py
├── requirements.txt
└── LICENSE
```

## Installation

Use Python 3.10 or newer. From this directory, install the listed dependencies:

```bash
python -m pip install -r requirements.txt
```

Install a PyTorch build compatible with the hardware and CUDA version used for training.


## Running representation completion

From the MARC repository root, pass the location of the authorized input CSV. The five-seed setting matches the formal analysis:

```bash
python -m tasks.run_completion \
  --data_path /path/to/authorized_features.csv \
  --feature_metadata_path data/feature_type.json \
  --output_dir results/latent_final \
  --run_five_seeds
```

Outputs are written to `results/latent_final/seed_0` through `seed_4`, including completed latent views, calibration records, configurations, and checkpoints.

## Running clustering

The default grid runs K=2 and K=3 for completion seeds 0–4, with clustering seed 0, using the formal training settings (200 reconstruction pretraining epochs, then 100 clustering epochs):

```bash
python -m tasks.run_clustering \
  --input_root results/latent_final \
  --output_root results/MARC
```

To run additional clustering seeds or K values, pass `--clustering_seeds` and `--k_values`. Outputs are organized under `results/MARC/reliability/seed_{n}/K_{k}/cluster_seed_{s}/`.

## Evaluating and building consensus phenotypes

```bash
python -m tasks.evaluate_clustering \
  --results_root results/MARC \
  --output results/MARC/evaluation

python -m tasks.build_consensus \
  --results_root results/MARC \
  --output results/MARC/consensus_labels.csv
```

Evaluation reports cosine silhouette, minimum cluster fraction, and pairwise ARI/NMI across runs. Consensus construction chooses the medoid partition by mean pairwise ARI, aligns cluster IDs with Hungarian matching, and resolves vote ties with the medoid labels.

## License

MARC code is released under the MIT License. This license does not grant rights to restricted input data or third-party materials.
