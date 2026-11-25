# CRPA: structural overlap and intervention sensitivity

This repository audits a sparse-attention experiment. It distinguishes structural
attention overlap from the measured loss change after removing one permitted
attention edge.

## Status

The original claim that overlap does not predict behavioural contribution is
withdrawn. The recorded single-edge loss estimates are not resolvable at the
reported precision: the best split-half reliability is 0.088, below the
predefined 0.3 threshold. The data cannot distinguish a weak relationship from
measurement noise.

The repository therefore supports a narrower result: at these settings,
single-edge intervention sensitivity is too small and unstable to justify a
contribution-gated selection rule. Historical results are retained as evidence,
not as support for the withdrawn claim.

## What is retained

- `crpa/`: model, sparse attention, interventions, data protocol, metrics,
  result records, and figure code.
- `experiments/`: one command-line module per experiment.
- `configs/`: 12.4M, 138M, and frozen-model profiles.
- `results/`: structured runs, aggregates, source data, figures, and clearly
  labeled historical artifacts.
- `tests/`: mechanism, split-separation, result-serialization, long-context,
  figure, and instrumentation tests.

An edge is `(layer, head, query, key)`. An intervention sets its pre-softmax
score to negative infinity for that layer and head, then renormalizes the row.
Calibration uses a disjoint split from final evaluation.

## Install and smoke test

```bash
pip install -r requirements.txt
python -m pytest -q -m "not slow"
python -m experiments.tier1_multiseed --smoke --synthetic_data --seeds 42
python -m experiments.matched_overlap --smoke --synthetic_data --seeds 42 --lambdas 0.0 0.1
python -m experiments.plot_all --results_dir results
```

Use `python -m pytest`, not bare `pytest`, on Windows environments where the
working directory is not added to the import path.

## Reproduce the studies

```bash
# Measurement-resolvability check; requires a GPU for the reported scale.
python -m experiments.resolvability --seeds 42 1337 2024 --loss lm --sweep

# Controlled small-model studies.
python -m experiments.tier1_multiseed --profile small_12m --seeds 42 1337 2024
python -m experiments.matched_overlap --profile small_12m --seeds 42 1337 2024 --tolerance 0.01
python -m experiments.overlap_vs_contribution --profile small_12m --seeds 42 1337 2024
python -m experiments.estimator_stability --profile small_12m --seeds 42

# 138M long-context diagnostic and measured benchmark.
python -m experiments.long_context --profile medium_138m --context_lengths 4096 8192 16384 32768 65536 --train_iters 0
python -m experiments.benchmark --profile medium_138m --context_lengths 4096 8192 16384 --dtype bfloat16 --device cuda

# Frozen large-model diagnostic. Optional packages are in requirements-optional.txt.
python -m experiments.large_model_diagnostic --model_id EleutherAI/pythia-6.9b --device cuda --dtype bfloat16 --context_length 1024 --n_candidates 64

# Regenerate every figure exclusively from structured results.
python -m experiments.plot_all --results_dir results
```

The small model has 12.4M parameters at 512 tokens. The 138M diagnostic was
run without training (`--train_iters 0`). The 6.9B diagnostic is a frozen-model
measurement. Neither is evidence that CRPA is faster than dense attention.

## Results and limitations

`results/resolvability/resolvability.json` records the primary withdrawal.
`results/tier1/` contains three-seed controlled runs; retrieval stayed below the
measured task floor. `results/tier2/` contains the completed 4k--64k
long-context diagnostic. Earlier trained partial and OOM Tier 2 records use
explicit `trained_partial_*` names. `results/tier1_lm/` contains the separate
language-model-loss intervention dataset, and `results/legacy_rowpair/` records
the historical row-pair semantics.

`results/tier3/` contains 192 interventions on frozen
Pythia-6.9B; edits changed logits but not the bfloat16 loss.

Figures are regenerated from CSV/JSON files and write their source data beside
the image. Status values distinguish completed, smoke, not-run, unsupported,
OOM, and failed executions. Aggregation excludes nonnumeric statuses.

The synthetic retrieval task has a measured floor near 52.8%, despite a 5%
uniform-token baseline. Three seeds and single-edge additive interventions are
limited evidence. A zero or weak observed correlation is not evidence of
independence when the estimator is unresolved.

## Historical artifacts

`results/original_published/` is an unchanged copy of the original committed
figures and tables. `results/original_reproduction/` records the failed attempt
to reproduce its contribution-gated headline. They are historical records,
not current conclusions.
