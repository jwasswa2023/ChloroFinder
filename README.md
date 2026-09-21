# Integrating Nontargeted Screening and Machine Learning for Enhanced Annotation and Prioritization of Plastic Degradation Products: A Demonstration Using Chlorinated Thermoplastic Polyurethane Plastics


This repository contains the code, trained machine learning model, reference data, and analytical workflows developed for ChloroFinder, a mass spectrometry-based framework for identifying and prioritizing chlorinated compounds in nontarget LC-HRMS/MS data.

ChloroFinder combines machine learning predictions from MS/MS fragmentation patterns with chlorine-specific mass spectrometric evidence, including MS1 chlorine isotope patterns and diagnostic MS/MS fragmentation rules. The framework was developed using reference spectra from MassBank and demonstrated using chlorinated transformation products generated from chemicals released during degradation of thermoplastic polyurethane (TPU) materials.

---

## Project Overview

Chlorinated transformation products can be difficult to identify in high-resolution mass spectrometry datasets because many features lack reference spectra or confident structural annotations.

ChloroFinder addresses this challenge by integrating complementary sources of evidence:

1. **Machine learning classification of MS/MS spectra**
2. **MS1 chlorine isotope patterns**
3. **HCl neutral loss**
4. **Cl radical loss**
5. **Chlorine-containing fragment pairs**
6. **Integrated evidence-based prioritization**

The machine learning component uses fragment ions and pairwise fragment mass differences (Δm/z) extracted from MS/MS spectra to distinguish chlorinated from non-chlorinated compounds.

The experimental workflow then combines the ChloroFinder probability with chlorine-specific evidence derived directly from LC-HRMS/MS data.

---

## Repository Structure

```text
ChloroFinder/
│
├── README.md
│
├── chlorofinder_massbank.pkl
│
├── MassBankEU_Positive_Fragmentation.csv
│
├── cleaned_labeled_fragments.csv
│
├── RSCRIPT_FOR_PATROON.ipynb
│
├── Chlorofinder code/
│
├── USING CHLOROFINDER/
│
├── Rule 1/
│
├── Rule 2/
│
├── Rule 3/
│
├── Rule 4/
│
├── Option B Code/
│
└── mzML_Files/

---

##  Workflow

```text
The overall ChloroFinder workflow is:





