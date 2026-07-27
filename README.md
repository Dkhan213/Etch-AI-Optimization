# Etch-AI: Bayesian Optimization for Semiconductor Plasma Etching

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![scikit-optimize](https://img.shields.io/badge/scikit--optimize-0.9.0-orange)
![Status](https://img.shields.io/badge/Status-Active-success)

## Overview
Etch-AI is a machine learning pipeline designed to optimize Reactive Ion Etching (RIE) recipes for semiconductor fabrication. Developed by Team Etch-a-Sketch during the SPARK-UP undergraduate research program at the University of Houston, this project uses Bayesian Optimization to navigate the highly non-linear physics of $SF_6 / O_2 / Ar$ plasmas. 

The algorithm is designed to find the optimal balance of chamber pressure, gas flow ratios, and RF bias power to maximize vertical etch rates while preserving the photoresist mask and maintaining anisotropic trench profiles.

## Hardware & Metrology Stack
* **Plasma Etcher:** Oxford System 100 (ICP-RIE)
* **Lithography:** ABM Mask Aligner (1,200 nm S1813 Photoresist)
* **Metrology:** Asylum MFP-3D Atomic Force Microscope (AFM)

## How It Works
Plasma physics is expensive and time-consuming to map manually. This project minimizes physical wafer waste by using a two-phase machine learning approach:

1. **Transfer Learning (The Prior):** The model is initially seeded with scaled historical data extracted from established plasma physics literature (Gomez 2004, d'Agostino 1981, Kim 2021). This teaches the AI the fundamental physical boundaries (e.g., the 25 mTorr pressure sweet spot and 1:1 $O_2/SF_6$ passivation ratio) before it ever touches the local cleanroom hardware.
2. **Active Learning (The Cleanroom):** After 3 physical runs, the algorithm discards the historical data and trains strictly on local AFM depth measurements. It uses an Expected Improvement (EI) acquisition function via `scikit-optimize` to actively explore unknown parameter spaces and exploit known peaks within strict safety bounds.

## Cleanroom Safety Bounds
The algorithm is hard-coded to restrict outputs to safe hardware limits, preventing mask destruction and severe undercutting:
* **RF Substrate Bias:** 25.0 W – 60.0 W
* **Chamber Pressure:** 10.0 mTorr – 30.0 mTorr
* **SF6 Gas Flow:** 20.0 SCCM – 45.0 SCCM
* **O2 Gas Flow:** 20.0 SCCM – 45.0 SCCM
* **Static ICP Power:** 800 W (Locked)

## Usage
1. Clone the repository and install the requirements:
   ```bash
   pip install scikit-optimize pandas numpy
