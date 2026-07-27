# Etch-AI: Bayesian Optimization for Semiconductor Plasma Etching

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![scikit-optimize](https://img.shields.io/badge/scikit--optimize-0.9.0-orange)
![Status](https://img.shields.io/badge/Status-Active-success)

## Overview
**Etch-AI** is an active machine learning pipeline designed to optimize Reactive Ion Etching (RIE) process parameters for silicon microfabrication. Developed by Team Etch-a-Sketch as part of the SPARK-UP undergraduate research program at the University of Houston, this project applies Bayesian Optimization to navigate the non-linear reaction dynamics of SF6 / O2 / Ar plasmas.

The primary objective is to maximize vertical silicon etch rates while preserving the protective photoresist mask and maintaining anisotropic, high-aspect-ratio trench profiles with minimal physical wafer waste.

---

## Hardware & Metrology Stack
* **Plasma Etcher:** Oxford System 100 (ICP-RIE)
* **Lithography:** ABM Mask Aligner (1,200 nm S1813 Photoresist)
* **Metrology:** Asylum MFP-3D Atomic Force Microscope (AFM)

---

## Architecture & Machine Learning Strategy

Mapping plasma chemistry manually requires extensive wafer runs and cleanroom tool time. Etch-AI accelerates discovery through a two-phase optimization pipeline:

1. **Transfer Learning (Historical Prior):** The model is initially seeded with normalized baseline data extracted from established plasma physics literature (Gomez et al. 2004[cite: 3], d'Agostino & Flamm 1981[cite: 4], Kim et al. 2021[cite: 2]). This teaches the AI fundamental non-linear trends—such as the ~25 mTorr pressure regime[cite: 3] and O2/SF6 passivation dynamics[cite: 3]—before performing local physical etches.
2. **Active Learning (Local Hardware Optimization):** Once 3 physical cleanroom runs are logged, the pipeline shifts to train exclusively on local AFM depth measurements from the Oxford System 100. It utilizes an Expected Improvement (EI) acquisition function via a Random Forest surrogate model in `scikit-optimize` to balance exploring uncertain parameter space and exploiting known high-performance peaks.

---

## Cleanroom Safety Window

The optimizer evaluates candidate recipes against strict hardware and mask-integrity boundaries to prevent photoresist destruction or severe undercut:

| Parameter | Boundary Range | Function |
| :--- | :--- | :--- |
| **RF Substrate Bias** | 25.0 W – 60.0 W | Controls vertical ion impact energy without sputtering mask |
| **Chamber Pressure** | 10.0 mTorr – 30.0 mTorr | Traps pressure around optimal mean free path regime |
| **SF6 Gas Flow** | 20.0 SCCM – 45.0 SCCM | Governs chemical fluorine radical density |
| **O2 Gas Flow** | 20.0 SCCM – 45.0 SCCM | Enables 1:1 gas ratios for sidewall oxide passivation |
| **ICP Source Power** | 800.0 W *(Locked)* | Controls plasma density independently as a static control |
| **Process Step Time**| 60 Seconds *(Locked)* | Prevents mask burnout and preserves AFM aspect ratios |

---

## Repository Structure

```text
etch-ai-optimization/
├── README.md
├── LICENSE
├── docs/
│   └── LAB_EXECUTION_GUIDE.md.md            # Your Phase 1–4 step-by-step lab instructions
├── data/
│   ├── DATASET_SUMMARY.md          # Complete breakdown of Gomez, d'Agostino, & Kim literature data
│   ├── historical_baseline.csv     # Literature prior dataset (27 records)
│   └── nanofab_experiments.csv     # Local cleanroom run logs
├── literature/
│   ├── Gomez_2004.pdf
│   ├── dAgostino_1981.pdf
│   └── Kim_2021.pdf
└── src/
    └── etch_optimizer.ipynb        # Core Bayesian Optimization notebook
