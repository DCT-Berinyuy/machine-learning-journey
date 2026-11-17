# 🛠️ Setup: running the course in Zed

I run the course notebooks locally in [Zed](https://zed.dev) instead of Colab. Zed's built-in REPL sends code to a Jupyter kernel and shows the output, plots included, right under the code.

## Folder layout

```text
Machine Learning/                 ← open this folder in Zed
├── .venv/                        ← Python environment (local only)
├── Colab_Notebooks_Days1-10/     ← course notebooks, .ipynb (not in this repo)
├── Zed_Notebooks_Days1-10/       ← the same notebooks as `# %%` scripts for Zed
└── machine-learning-journey/     ← this repo
```

## One-time setup

Run these from the `Machine Learning/` folder:

```sh
python -m venv .venv
.venv/bin/pip install -r machine-learning-journey/requirements.txt
.venv/bin/python -m ipykernel install --user --name ml-journey --display-name "Python (ML journey)"
```

Zed can't open `.ipynb` files as notebooks, but it runs *notebooks as scripts*: a `.py` file split into cells by `# %%` lines. Convert the course notebooks once, and again whenever new ones arrive:

```sh
mkdir -p Zed_Notebooks_Days1-10
for nb in Colab_Notebooks_Days1-10/*.ipynb; do
  .venv/bin/jupytext --to py:percent -o "Zed_Notebooks_Days1-10/$(basename "${nb%.ipynb}").py" "$nb"
done
```

## Daily use

1. Open the `Machine Learning/` folder in Zed.
2. Open the day's file in `Zed_Notebooks_Days1-10/`. The notebook's text appears as comments.
3. Put the cursor in a cell and press **Ctrl+Shift+Enter** (`repl: run`). The output appears under the cell.
4. To add a cell of your own, type a new `# %%` line and write code below it.
5. If the output says a module is missing, pick the **Python (ML journey)** kernel from the REPL menu in the toolbar.

## TensorFlow days (7, 8 and 10)

TensorFlow 2.21 only supports Python 3.10–3.13, and this environment uses Python 3.14, so those notebooks won't run here yet. The plan:

- **Days 7 and 10** train tiny models, so a second environment with Python 3.13 and TensorFlow can run them on a laptop CPU.
- **Day 8** fine-tunes MobileNetV2 on about 3,700 photos, which is much faster on the free GPU in Colab or Kaggle.

## Checked

Days 1–6 and 9 run through the kernel with no errors, plots included (Python 3.14, Zed 1.19, numpy 2.5, scikit-learn 1.9, scikit-image 0.26). Day 9 prints exactly the numbers in Workbook Part 2: 97% in the lab, 13% when shifted, and the same recall for every digit.
