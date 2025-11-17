# Part 1: Calling Native Waveform Implementations (Tutorial + Notebook)

This Markdown file accompanies the notebook [**GW\_Waveform\_Zoo\_Native\_Implementations.ipynb**](https://suyoggarg.com/ewha/notebooks/GW_Waveform_Zoo_Native_Implementations.ipynb)

## Quick-start (conda or Colab)

Conda (recommended):

```bash
mamba create -n gw-waveforms python=3.11 -c conda-forge -y
mamba activate gw-waveforms
mamba install pycbc gwsurrogate teobresums -c conda-forge -y
pip install pyseobnr
```

Colab: upload the notebook and run all cells. The first cell performs `pip install`.

## What it covers

- **PyCBC/LALSimulation** models: TaylorF2/F2Ecc, SpinTaylorT4, IMRPhenom*, SEOBNRv4/v4HM, SEOBNRv4_ROM,
  Phenom+NRTidalv2, SEOBNRv4T_surrogate.
- **pySEOBNR**: SEOBNRv5HM / SEOBNRv5PHM / SEOBNRv5EHM.
- **TEOBResumS** via EOBRun Python bindings.
- **GWSurrogate**: NRSur7dq4, NRHybSur3dq8.

The notebook also benchmarks generation time across families for `n=6` random draws per model.

---