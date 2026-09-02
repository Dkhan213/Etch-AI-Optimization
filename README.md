# Etch-AI: Bayesian Optimization for Semiconductor Plasma Etching

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![scikit-optimize](https://img.shields.io/badge/scikit--optimize-0.9.0-orange)
![Status](https://img.shields.io/badge/Status-Active-success)

## Overview
**Etch-AI** is an active machine learning pipeline designed to optimize Reactive Ion Etching (RIE) process parameters for silicon microfabrication. Developed by Team Etch-a-Sketch during an R&D engineering internship as part of the SPARK-UP undergraduate research program at the University of Houston, this project applies Bayesian Optimization to navigate the non-linear reaction dynamics of SF6 / O2 / Ar plasmas.

The primary objective is to maximize vertical silicon etch rates while preserving the protective photoresist mask and maintaining anisotropic, high-aspect-ratio trench profiles with minimal physical wafer waste.

The problem: a modern RIE tool exposes dozens of interacting process parameters, and changing any one of them alters the physics of the entire plasma. Finding a working recipe traditionally means running an etch, pulling the wafer, inspecting it, adjusting, and repeating, burning expensive cleanroom tool time on blind trial and error. Etch-AI replaces that loop with a model that proposes the next recipe to run.

---

## Hardware & Metrology Stack
* **Plasma Etcher:** Oxford System 100 (ICP-RIE)
* **Lithography:** ABM Mask Aligner (1,200 nm S1813 Photoresist)
* **Metrology:** Asylum MFP-3D Atomic Force Microscope (AFM)

---

## Architecture & Machine Learning Strategy

Mapping plasma chemistry manually requires extensive wafer runs and cleanroom tool time. Etch-AI accelerates discovery through a three-phase optimization pipeline:

1. **Transfer Learning (Historical Prior):** The model is initially seeded with normalized baseline data extracted from established plasma physics literature (Gomez et al. 2004, d'Agostino & Flamm 1981, Kim et al. 2021). Etch rates are min-max scaled so the model learns relative physical trends, such as the ~25 mTorr pressure regime and O2/SF6 passivation dynamics, without being distorted by absolute rates from differing hardware. In effect, the model reads the textbook before it touches the equipment.
   
2. **Design of Experiments (Local Calibration):** Historical data alone is insufficient: every tool behaves differently, and 100 W on a 2009-era chamber is not 100 W on ours. Rather than sweeping the parameter space exhaustively, a small structured set of runs characterizes the local machine, the extremes (corners) of the parameter space, plus replicated center points to capture baseline behavior and run-to-run variance. This maps the response surface in a handful of runs instead of hundreds.
   
3. **Active Learning (Local Hardware Optimization):** Once 3 physical cleanroom runs are logged, the pipeline trains exclusively on local AFM depth measurements from the Oxford System 100. A Random Forest surrogate model in `scikit-optimize` builds a predictive map of the chamber; candidate recipes are then generated within the cleanroom safety window and scored against that surrogate, with the best-predicted safe recipe selected for the next physical run. Each completed run feeds back into the model, so the system self-corrects as the tool drifts.

---

## Cleanroom Safety Window

The optimizer evaluates candidate recipes against strict hardware and mask-integrity boundaries to prevent photoresist destruction or severe undercut. Candidates are sampled inside these bounds, so no proposed recipe can exceed them:

| Parameter | Boundary Range | Function |
| :--- | :--- | :--- |
| **RF Substrate Bias** | 25.0 W – 60.0 W | Controls vertical ion impact energy without sputtering mask |
| **Chamber Pressure** | 10.0 mTorr – 30.0 mTorr | Traps pressure around optimal mean free path regime |
| **SF6 Gas Flow** | 20.0 SCCM – 45.0 SCCM | Governs chemical fluorine radical density |
| **O2 Gas Flow** | 20.0 SCCM – 45.0 SCCM | Enables 1:1 gas ratios for sidewall oxide passivation |
| **ICP Source Power** | 800.0 W *(Locked)* | Controls plasma density independently as a static control |
| **Process Step Time**| 60 Seconds *(Locked)* | Prevents mask burnout and preserves AFM aspect ratios |

---

## Running the Pipeline

The optimizer runs as a self-contained notebook and pulls its datasets directly from this repository, so no local data setup is required.

1. Open `src/etch_optimizer.ipynb` in Google Colab or Jupyter.
2. Run all cells. The notebook installs `scikit-optimize` and loads both the historical prior and the local run log from `data/`.
3. The output prints a complete recipe for the next physical run — static parameters plus the model-selected RF bias, chamber pressure, SF6 flow, and O2 flow.
4. After executing that run, append the measured etch rate to `data/nanofab_experiments.csv` and re-run to generate the next recommendation.

See `docs/LAB_EXECUTION_GUIDE.md` for the full phase-by-phase lab procedure.

---

## Repository Structure

```text
etch-ai-optimization/
├── README.md
├── docs/
│   └── LAB_EXECUTION_GUIDE.md      # Phase 1–4 step-by-step lab instructions
├── data/
│   ├── Reference.md
│   ├── historical_baseline.csv     # Literature prior dataset (27 records)
│   └── nanofab_experiments.csv     # Local cleanroom run logs
├── literature/
│   ├── Gomez_2004.pdf
│   ├── dAgostino_1981.pdf
│   └── Kim_2021.pdf
└── src/
    └── etch_optimizer.ipynb        # Core Bayesian Optimization notebook
```

## Project Walkthrough

[Bayesian Optimization in Nanofabrication](https://drive.google.com/file/d/19UxLR0r06BqqKRvVcAlex2tw5dnSL1Z4/view?usp=drive_link) — a full walkthrough of the cleanroom fabrication workflow and the optimization pipeline.
