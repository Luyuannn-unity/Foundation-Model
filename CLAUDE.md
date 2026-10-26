# CLAUDE.md

Shared context for every Claude session in this repo. Keep it short and current.
Changes to this file go through a pull request reviewed by a human.

## Project

We are training a foundation model for <DOMAIN / MODALITY>.
Goal: <one or two sentences: what the model should do and how we'll judge it>.
Team: <names and who owns what, e.g. data prep / architecture / training infra>.

## Repo layout

```
data_prep/      # scripts that turn raw data into versioned, sharded datasets
model/          # architecture code
train/          # training loop, distributed setup, checkpointing
eval/           # evaluation and benchmarks
configs/        # YAML configs: the single source of truth for experiments
tests/          # unit, shape and smoke tests
docs/
  decisions/    # numbered decision records (read before changing related code)
  data.md       # dataset versions, locations, provenance
  architecture.md
```

## Before you start a task

1. Read the decision records in `docs/decisions/` that relate to your task.
2. Check `docs/data.md` for the current dataset version.
3. If your change contradicts an existing decision, stop and flag it to the human.
   Do not quietly change it. Propose a new decision record instead.

## Commands

```bash
# environment
<e.g. uv sync / conda env create -f environment.yml>

# tests (run before saying a task is done)
pytest tests/

# smoke test: a few training steps on a tiny subset, must pass before any PR
python -m train.run --config configs/smoke.yaml

# full training run
python -m train.run --config configs/<experiment>.yaml
```

## Data

- Data lives on the institution OneDrive at: `<path>`. It is NOT in this repo.
- `raw/` is read-only. Never modify, move or delete it.
- Processed versions live in `processed/vN_YYYY-MM-DD/` and are immutable once created.
  To change anything, write a new version via a script in `data_prep/`.
- Every processed version has a `manifest.json` (source version, git commit, file count,
  checksums). Generate it with `<script>`.
- Current version for training: `<vN>` (see `docs/data.md`).
- Copy data to local/cluster scratch before training; do not train from the synced folder.

## Conventions

- All hyperparameters and paths go in `configs/`, never hard-coded.
- Every run logs to <W&B / MLflow project name> with its config, git commit and data version.
- Set and log random seeds.
- Add tests for new data transforms (input/output check) and model components (shape check).
- Type hints on public functions. Format with `<ruff / black>`.
- One task per branch. Keep PRs small.

## Hard rules

- Never delete or overwrite data, checkpoints or experiment logs.
- Never commit data, checkpoints or credentials to git.
- Never launch a full or multi-GPU training run without the human's go-ahead.
- Don't change files in `docs/decisions/` that are already accepted; add a new one.
- If a test fails or a result looks wrong, report it. Don't paper over it.

## Writing things down

- New design decision → draft `docs/decisions/NNNN-short-title.md`
  (context, decision, alternatives considered, consequences) for human review.
- Model design changed → update `docs/architecture.md` in the same PR.
- In-progress plans go in GitHub issues, not in this file.

## Current status

<2–4 lines: what phase we're in and what's next. Update when it changes.>
