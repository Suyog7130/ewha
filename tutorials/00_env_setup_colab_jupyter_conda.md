# Environment setup for the tutorial

*Author: Suyog Garg, Dated: 2025/11/18*

---

This document is an **introductory basics part** for your tutorial series.  
It covers three things:

1. Installing **Anaconda** on macOS, Linux, and Windows.
2. Creating and using a **conda environment** with Jupyter Notebook and JupyterLab to run Parts 1–3.
3. A gentle introduction to **Google Colab** for students who prefer a cloud option.

> You do **not** need Anaconda if you will only use Google Colab.  
> For local work on your own laptop or a lab machine, Anaconda is recommended.

## 1. Installing Anaconda

### 1.1 What is Anaconda

Anaconda is a Python distribution that includes Python itself plus the `conda` package and environment manager.  
We will use it to create a clean environment for the tutorial, without touching the system Python.

### 1.2 Downloading the installer

On any operating system:

1. Open your web browser.
2. Go to the official Anaconda Distribution download page.
3. Choose the installer for your OS and CPU architecture (Windows, macOS Intel, macOS Apple Silicon, Linux).
4. Download the installer file to your computer.

The exact filenames change over time, but they look something like:

- `Anaconda3-202x.xx-Windows-x86_64.exe`
- `Anaconda3-202x.xx-MacOSX-x86_64.pkg` or `Anaconda3-202x.xx-MacOSX-arm64.pkg`
- `Anaconda3-202x.xx-Linux-x86_64.sh`

### 1.3 Installing on Windows

1. Double click the downloaded `.exe` file.
2. When the installer asks who to install for, choose **"Just Me"** unless you have admin rights and want a system wide install.
3. Accept the license and keep the default install location (for example `C:\Users\yourname\anaconda3`).
4. When asked about **"Add Anaconda to my PATH environment variable"**, it is usually fine to leave this **unchecked** and let the installer manage an **Anaconda Prompt** instead.
5. Finish the installation.
6. Open the **Anaconda Prompt** from the Start menu and type:

   ```bash
   conda --version
   ```
   
   If you see a version string, the install is working.

### 1.4 Installing on macOS

1. If you downloaded a `.pkg` installer:
   - Double click the `.pkg` file and follow the GUI steps.
   - Accept the license, keep the default install path (for example `/Users/yourname/anaconda3`).

2. If you downloaded a `.sh` installer:
   - Open **Terminal**.
   - Change directory to where you downloaded the installer, for example:
   
     ```bash
     cd ~/Downloads
     bash Anaconda3-202x.xx-MacOSX-x86_64.sh
     ```
     
   - Press Enter to scroll through the license, type `yes` to accept.
   - Press Enter to accept the default install location.
   - When the installer asks to run `conda init`, answer `yes`.

3. Close and reopen the Terminal window, then run:

   ```bash
   conda --version
   ```
   
   If a version number appears, Anaconda is correctly installed.

### 1.5 Installing on Linux

1. Open a terminal.
2. Change directory to where you downloaded the `.sh` installer, for example:

   ```bash
   cd ~/Downloads
   bash Anaconda3-202x.xx-Linux-x86_64.sh
   ```
   
3. Read or scroll through the license and type `yes` to accept.
4. Accept the default install path (for example `/home/yourname/anaconda3`).
5. When asked whether to run `conda init`, answer `yes`.
6. Close and reopen the terminal, then run:

   ```bash
   conda --version
   ```
   
   If you see a version number, everything is set.

---

## 2. Creating a conda environment for the tutorial

We will create one environment and reuse it for **all parts** of the tutorial.

You can choose any name. In this guide we use `gwml-env`.

```bash
conda create -n gwml-env python=3.11 -y
```

Activate it:

```bash
conda activate gwml-env
```

From now on, whenever you work on the tutorial in a terminal, first activate this environment with `conda activate gwml-env`.

> Part 1 of your tutorial may already describe basic `pip install` steps.  
> The important thing is that those `pip` commands are always run **inside** this environment.

---

## 3. Installing Part 3 requirements locally

Part 3 (CNN on MNIST with Keras, TensorFlow, PyTorch, scikit-learn) needs extra libraries that are listed in `Part3_requirements.txt`:

```text
numpy
matplotlib
scikit-learn
scikeras
tensorflow
torch
torchvision
```

Make sure you have activated the environment first:

```bash
conda activate gwml-env
```

Then, in the folder where your tutorial repository lives (and where `part3_requirements.txt` is stored), run:

```bash
pip install -r Part3_requirements.txt
```

This installs all the Part 3 dependencies into the same environment.  
You only need to run this once per environment.

If you prefer conda for some packages (for example to get GPU builds of PyTorch or TensorFlow), you can mix and match:

```bash
# Example: CPU baseline from conda-forge
conda install -c conda-forge numpy matplotlib scikit-learn scikeras -y
# Then use pip only for framework builds you want
pip install tensorflow
pip install torch torchvision
```

The exact commands for GPU enabled frameworks depend on your GPU and drivers, but the basic idea is the same: install everything while `gwml-env` is active.

---

## 4. Using Jupyter Notebook with the conda environment

### 4.1 Installing Jupyter Notebook

If Jupyter Notebook is not already installed in your environment:

```bash
conda activate gwml-env
pip install notebook
```

Or via conda:

```bash
conda activate gwml-env
conda install notebook -y
```

### 4.2 Starting Notebook and choosing the correct kernel

1. Activate the environment:

   ```bash
   conda activate gwml-env
   ```
   
2. Start Jupyter Notebook:

   ```bash
   jupyter notebook
   ```
   
3. A browser window will open. Navigate to the folder where your Part 1, Part 2, and Part 3 `.ipynb` files live.
4. Open the notebook you want to run.
5. Ensure the kernel is the one for `gwml-env`:
   - In the menu choose **Kernel -> Change kernel** and pick the Python kernel that corresponds to this environment.
   - If you do not see a separate kernel, you can add one with:
   
     ```bash
     python -m ipykernel install --user --name gwml-env --display-name "Python (gwml-env)"
     ```
     
     after which it will appear in the kernel list.

Once the right kernel is selected, you can run all cells, including the ones that use TensorFlow, PyTorch, Keras, and scikit-learn.

---

## 5. Using JupyterLab with the conda environment

The steps are almost the same as for Notebook.

### 5.1 Install JupyterLab

Inside the environment:

```bash
conda activate gwml-env
pip install jupyterlab
# or
conda install jupyterlab -y
```

### 5.2 Start JupyterLab

```bash
conda activate gwml-env
jupyter lab
```

JupyterLab will open in your browser. From there:

1. Use the file browser on the left to open your tutorial notebooks.
2. When you create or open a notebook, choose the **"Python (gwml-env)"** kernel from the launcher or the kernel menu.

Since the same environment is used for both Notebook and Lab, the libraries installed by `pip install -r part3_requirements.txt` are available in both.

---

## 6. Using Google Colab

Google Colab is a free cloud based environment provided by Google.  
It runs Jupyter style notebooks in your browser on a remote machine. You do not need to install Python or Anaconda locally to use it.

### 6.1 Accessing Colab

1. Open a browser and go to the Google Colab site.
2. Sign in with your Google account.
3. You can create a **New notebook** or open existing notebooks from:
   - Google Drive (if you uploaded them there),
   - GitHub (by giving a repo URL),
   - or by uploading a `.ipynb` file directly.

### 6.2 Running cells

- Each notebook has cells that you can run with the **play** button or by pressing `Shift+Enter`.
- Outputs appear directly below the cell (text, plots, etc.).

### 6.3 Installing extra packages in Colab

Colab comes with many libraries preinstalled (NumPy, matplotlib, TensorFlow, etc.), but not always the exact versions you want.

You can install extra packages at the top of the notebook with a special cell:

```python
# Colab only: install or upgrade tutorial dependencies
!pip install -q scikeras torch torchvision
```

Use `!pip install` only in Colab notebooks. For local Anaconda environments, install with `pip` or `conda` in your terminal instead.

### 6.4 Using GPU or TPU in Colab (optional)

If you want to accelerate training in Part 3:

1. In the menu go to **Runtime -> Change runtime type**.
2. Set **Hardware accelerator** to **GPU** or **TPU**.
3. Save and rerun the first cells.

Your code can then check for GPU devices using the normal TensorFlow or PyTorch APIs.

## 7. Summary

- Install Anaconda once per machine.
- Create a dedicated environment, for example `gwml-env`.
- Install tutorial dependencies (`Part3_requirements.txt`) **inside** that environment.
- Use Jupyter Notebook or JupyterLab from the same environment to run Parts 1 to 3.
- If a student prefers not to install anything, they can use Google Colab and run the same notebooks there (with a small `!pip install` cell at the top).

---