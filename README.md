 Physics-Informed RUL for Bearings: 17-Fold LOBOCV

Code accompanying the manuscript *"Physics Priors Shape but Do Not Demonstrate Bearing RUL Accuracy Improvement Under Strict Bearing-Level Holdout: A 17-Fold Cross-Validation Study"* (submitted to IJPHM).

**Main file:** `mt_supinn.py` — the full pipeline: data loading, model, training, ablation, and LOBOCV.

## Requirements

- Python 3.10+
- TensorFlow 2.13+
- See `requirements.txt` for the full list

## Data

Three public datasets are expected in these paths (edit `ConfigV3` in the code to match your local layout):

- **CWRU** — `/kaggle/input/datasets/pleiades74/my-cwru/csv_files`
- **Paderborn** — `/kaggle/input/datasets/pleiades74/multimodel-pader/Paderborn`
- **FEMTO-PRONOSTIA** — `/kaggle/input/datasets/egorefimow/smallone/Pronostia dataset`

All three are publicly available on Kaggle. No data is included in this repository.

## How to reproduce

The pipeline runs in stages controlled by `RUN_STAGE` at the bottom of `mt_supinn.py`.

```python
# In mt_supinn.py, bottom of file
RUN_STAGE = 1    # 1=Data+Generators, 2=Ablation, 4=UQ, 5=PHY, 6=Robustness, 7=Figures
Reproduce the four protocols in the paper:

Stage 1 — load data, fit EWS scaler, compute per-bearing α, build generators. Saves stage1.pt (~430 MB).

python
RUN_STAGE = 1
Run time: ~10 min.

Stage 2 — main ablation A0–A5 on 6 folds. Saves results_E.json and model weights.

python
RUN_STAGE = 2
Run time: ~4–5 hours.

LOBOCV — 17-fold leave-one-bearing-out cross-validation with optional target noise and clustering ablation. Controlled by run_lobocv() at the bottom of the file.

python
# 17-fold noisy, A0 / A2
run_lobocv(
    folds_to_run=None,
    arms_to_run=['A0_no_physics', 'A2_adaptive_physics'],
    epochs_override=40,
    target_noise_sigma=0.05,
)

# 17-fold noisy, A6 (physics without clustering)
run_lobocv(
    folds_to_run=None,
    arms_to_run=['A6_no_clustering'],
    epochs_override=40,
    target_noise_sigma=0.05,
)
Each arm × 17 folds: ~6 hours.

Results are cached per (fold, arm) as JSON under lobocv_<variant>/. Reruns skip completed entries.

Hardware
All experiments were run on a single Kaggle T4 GPU with ~13 GB memory. Full reproduction (all four protocols) takes ~30 hours of compute.

Seeds
All reported results use CURRENT_SEED = 42 (top of file). Change this value to run additional seeds.

Contact
Thuraya Mohammed Majed Shaheen — shaheenthuraya@gmail.com
Higher School of Cyber-Physical Systems and Control, Peter the Great St. Petersburg Polytechnic University
