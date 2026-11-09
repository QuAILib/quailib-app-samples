# qklearn / SWIFT-FMQA / Qudas Tutorial (MVTec AD Anomaly Detection)

English | [日本語](README.md)

This hands-on notebook uses the `tile` category of MVTec AD to let you experience
image anomaly detection and hyperparameter search by combining the following three libraries.

- **qklearn** (Qukit-learn): formulates clustering as a QUBO and solves it with Amplify
- **Qudas**: converts a QUBO into other formats such as dimod and Amplify Poly
- **SWIFT-FMQA** (`amplify_bbopt`): black-box optimization with a Factorization Machine surrogate model,
  used here to search for hyperparameters

This tutorial does not use real quantum hardware. QUBOs are solved with Fixstars Amplify.

## Files

| File | Contents |
|---|---|
| `clustering_qklearn_fmqa_tutorial.ipynb` | The hands-on notebook in Japanese (run it from top to bottom) |
| `clustering_qklearn_fmqa_tutorial_en.ipynb` | The English version of the notebook (identical code and results) |
| `README.md` | The Japanese README |
| `manifests/candidates/tile_manifest.csv` | The images used and their assignment to training, threshold calibration, Validation and Test (does not contain the images themselves) |
| `requirements-lock.txt` | List of dependencies with pinned versions |

## Requirements

1. **Python 3.11** (the only version this has been verified with)
2. **A Fixstars Amplify API token**
   Get a free token at https://amplify.fixstars.com/register and set it in the environment variable `AMPLIFY_TOKEN`.
   Do not write the token into the notebook or any file.
3. **MVTec AD (`tile` category)**
   **It is not included in this repository.** Download it yourself as described below.
4. A network that can reach GitHub (`github.com`)
   (`pip install` fetches `qklearn` and `amplify-bbopt-ext` from GitHub)

## Setup

```bash
# 1. Create a virtual environment (Python 3.11)
python3.11 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

# 2. Install the dependencies
pip install -r requirements-lock.txt

# 3. Register a Jupyter kernel (any name will do)
python -m ipykernel install --user --name qklearn_fmqa --display-name "Python 3.11 (qklearn_fmqa)"

# 4. Set your Amplify token (replace the value with your own token)
export AMPLIFY_TOKEN="..."         # Windows PowerShell: $env:AMPLIFY_TOKEN = "..."
```

## Preparing MVTec AD

MVTec AD is released under the **CC BY-NC-SA 4.0** license. **It may be used only for non-commercial research and educational purposes, and it may not be redistributed.**
Please check the terms of use and download it yourself.

1. Download the dataset from the official page.
   https://www.mvtec.com/company/research/datasets/mvtec-ad
2. Create `data/MVTecAD/` in this folder and extract the archive there.
   This tutorial uses only the `tile` category.
3. You are ready when the structure looks like this.

```text
data/MVTecAD/tile/
├─ train/good/
├─ test/{good,crack,glue_strip,gray_stroke,oil,rough}/
└─ ground_truth/{crack,glue_strip,gray_stroke,oil,rough}/
```

If you keep the data elsewhere, set the environment variable `MVTEC_ROOT` to the folder one level above the `tile` folder.
If `data/MVTecAD` exists, it takes precedence.

## How to run

1. Start Jupyter in this folder (for example `jupyter lab`).
   Make sure the current directory is the folder that contains the notebook.
2. Open `clustering_qklearn_fmqa_tutorial_en.ipynb` and select the kernel you registered
   (`Python 3.11 (qklearn_fmqa)` in the example above).
3. Run the cells from top to bottom.

The features (MobileNetV2) are computed on the first run and saved to `cache/` (later runs reuse them).
The notebook calls the Amplify cloud dozens of times, so the whole run takes a few minutes.
Amplify solves have a time limit, so the numbers may change slightly from run to run.

## Running on ABCI-Q

The Amplify AE settings are different. The steps are described in the code comments of Chapter 4.
Official ABCI-Q documentation (Japanese): https://g-quat-abciq.github.io/abciq-docs/ja/apps/amplify-ae/

## License and data

- The code in this repository follows the `LICENSE` file in the repository root.
- MVTec AD is under a separate license (CC BY-NC-SA 4.0). The data is not included in this repository;
  each user must obtain it from the official page.
- `tile_manifest.csv` lists the file names, hash values and assignments of the images (it does not contain image data).
