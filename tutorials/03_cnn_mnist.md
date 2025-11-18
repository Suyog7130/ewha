
# Part 3: End‑to‑End CNN on MNIST (Keras • scikit‑learn • TensorFlow • PyTorch)

*Author: Suyog Garg, Dated: 2025/11/18*

---

Accompanying notebooks:

- Notebook A - Keras: [Part3A\_Keras\_MNIST\_CNN.ipynb](https://suyoggarg.com/ewha/notebooks/Part3A_Keras_MNIST_CNN.ipynb)
- Notebook B - scikit‑learn (via SciKeras): [Part3B\_ScikitLearn\_SciKeras\_MNIST\_CNN.ipynb](https://suyoggarg.com/ewha/notebooks/Part3B\_ScikitLearn\_SciKeras\_MNIST\_CNN.ipynb)
- Notebook C - TensorFlow Core (GradientTape): [Part3C\_TensorFlowCore\_MNIST\_CNN.ipynb](https://suyoggarg.com/ewha/notebooks/Part3C\_TensorFlowCore\_MNIST\_CNN.ipynb)
- Notebook D - PyTorch: [Part3D\_PyTorch\_MNIST\_CNN.ipynb](https://suyoggarg.com/ewha/notebooks/Part3D\_PyTorch\_MNIST_CNN.ipynb)
- Comparison notebook: [Part3E\_Compare\_Frameworks.ipynb](https://suyoggarg.com/ewha/notebooks/Part3E_Compare_Frameworks.ipynb) (reads `artifacts/*_metrics.json`)

All notebooks save plots and metrics into `./artifacts/` for later comparison.

## 0) Why four mechanisms?

- **Keras**: shortest code and fastest path to a solid CNN; batteries included.
- **scikit‑learn** (+ **SciKeras**): lets you use sklearn’s `fit/score`, CV, and grid‑search around a Keras CNN.
- **TensorFlow Core**: full control over training (for custom loops, research, mixed precision, etc.).
- **PyTorch**: explicit, pythonic training loop; great research ergonomics and ecosystem.

The comparison notebook summarizes **test accuracy**, **train time**, **parameter count**, and an approximate
**effective LOC** (lines of code, measured by introspecting key functions).

---

## 1) Quick start (local or Colab)

**Colab:** upload any notebook, run all.  
**Local conda env (CPU baseline):**

```bash
conda create -n mnist-cnn python=3.11 -c conda-forge -y
conda activate mnist-cnn
conda install numpy matplotlib scikit-learn scikeras tensorflow pytorch torchvision -c conda-forge -y
```

> GPU users should install vendor wheels:
>
> - **PyTorch (CUDA):** see `https://pytorch.org/get-started/locally/` and pick the `--index-url` for your CUDA version.
> - **TensorFlow (CUDA):** install a build matching your CUDA/cuDNN.
> - **Apple M‑series (MPS):** recent PyTorch supports `mps`; TensorFlow uses the **metal** plugin on macOS.

---

## 2) Running on a remote server (CPU / MPS / CUDA)

### 2.1 Headless Jupyter over SSH

```bash
# On remote
conda activate mnist-cnn
jupyter lab --no-browser --port 8890

# On your laptop
ssh -L 8890:localhost:8890 youruser@remote.host
# Then open http://localhost:8890
```

### 2.2 Device selection

- **PyTorch**: the notebooks auto-detect `cuda` → `mps` → `cpu`:

  ```python
  device = torch.device("cuda" if torch.cuda.is_available() else ("mps" if torch.backends.mps.is_available() else "cpu"))
  ```
  
- **TensorFlow / Keras**:

  ```python
  import tensorflow as tf
  tf.config.list_physical_devices()   # shows CPU/GPU/MPS
  ```
  
- If multiple GPUs exist, you can select visibility by `CUDA_VISIBLE_DEVICES=0 python ...` (PyTorch & TF).

### 2.3 CUDA vs CPU vs MPS

- **CUDA (NVIDIA)**: fastest training, ensure the installation wheel matches driver/CUDA runtime.
- **MPS (Apple)**: good speedups vs CPU; feature coverage still maturing.
- **CPU**: slower but perfectly fine for MNIST demos.

---

## 3) Outline of the session

1. Start with **A (Keras)**: quick win, show loss/accuracy plots.
2. Continue **B (scikit‑learn)**: fit/score API + tiny grid search.
3. Then use **C (TF Core)**: how to customize training with `GradientTape` etc.?
4. Finally with **D (PyTorch)**: point out explicit training loops and device placement.
5. Run **E (Compare)** to produce the summary plots/table from the `artifacts/*_metrics.json` files.

## 4) Tips

- Keep the same **batch size** and **epochs** when comparing speed.
- For reproducibility, set `numpy` and framework seeds if you need bitwise‑stable results.
- To try your CNN on **Fashion‑MNIST** dataset, change the dataset imports, the rest of the code stays the same.

Enjoy!

---
