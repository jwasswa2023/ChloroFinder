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

---

## Workflow

The ChloroFinder workflow integrates machine learning with chlorine-specific mass spectrometric evidence to identify and prioritize candidate chlorinated compounds in LC-HRMS/MS data.

The overall workflow consists of three major components:

1. **Development of the ChloroFinder machine learning classifier**
2. **Evaluation of chlorine-specific MS1 and MS/MS evidence**
3. **Integration of complementary evidence for candidate prioritization**

```text
Reference MS/MS spectra
        │
        ▼
MassBank spectral preprocessing
        │
        ▼
Chlorinated / non-chlorinated labels
        │
        ▼
Fragment ions + Δm/z features
        │
        ▼
Random Forest classifier
        │
        ▼
ChloroFinder probability
        │
        ├──────────────────────────────┐
        │                              │
        ▼                              ▼
MS1 chlorine isotope evidence     MS/MS chlorine rules
(M, M+2, M+4)                     HCl loss
                                  Cl radical loss
                                  Cl-containing fragment pairs
        │                              │
        └──────────────┬───────────────┘
                       ▼
              Evidence integration
                       │
                       ▼
          Prioritized chlorinated
             candidate features
```

---

## 1. Reference Spectral Data

ChloroFinder was developed using positive-mode MS/MS reference spectra obtained from MassBank.

The reference dataset contains spectra from both chlorinated and non-chlorinated compounds, allowing the machine learning model to learn fragmentation characteristics associated with chlorine-containing compounds.

The repository includes the reference and processed datasets used during model development:

```text
MassBankEU_Positive_Fragmentation.csv
cleaned_labeled_fragments.csv
```

These data were processed to extract fragment ions and pairwise fragment mass differences for machine learning.

---

## 2. ChloroFinder Machine Learning Model

ChloroFinder uses a Random Forest classifier trained on two groups of features extracted from reference MS/MS spectra:

### Fragment ions

Product-ion m/z values are extracted from each MS/MS spectrum and rounded to three decimal places before feature encoding.

### Pairwise Δm/z features

For each spectrum, pairwise mass differences between observed fragment ions are calculated:

```text
Δm/z = |m/z_i - m/z_j|
```

These Δm/z features capture relationships among fragment ions that may contain additional information about fragmentation behavior beyond the individual fragment masses.

The fragment-ion and Δm/z feature matrices are combined and used as input to the Random Forest classifier.

The trained ChloroFinder model is provided in the repository as:

```text
chlorofinder_massbank.pkl
```

The model bundle contains the trained classifier and the feature encoders required to transform new MS/MS spectra using the same fragment and Δm/z feature space used during model development.

---

## 3. Applying ChloroFinder to Experimental Data

The trained ChloroFinder model can be applied to experimental MS/MS spectra to estimate the probability that a spectrum is consistent with fragmentation patterns learned from chlorinated reference compounds.

The application workflow:

1. Extracts MS/MS fragment ions.
2. Rounds fragment m/z values to three decimal places.
3. Calculates pairwise Δm/z values.
4. Maps experimental fragments and Δm/z values to the trained feature vocabulary.
5. Applies the trained Random Forest classifier.
6. Generates a ChloroFinder probability for each feature.

The current workflow retains three probability thresholds:

```text
Broad threshold:    ≥ 0.40
Default threshold:  ≥ 0.50
High threshold:     ≥ 0.60
```

The continuous ChloroFinder probability is retained for integration with independent mass spectrometric evidence.

---

## 4. Chlorine-Specific Mass Spectrometric Evidence

In addition to the machine learning classifier, ChloroFinder uses four chlorine-specific mass spectrometric rules to provide complementary experimental evidence.

### Rule 1: MS1 Chlorine Isotope Pattern

Rule 1 evaluates characteristic chlorine isotope patterns in MS1 spectra.

Chlorine has two major naturally occurring isotopes, **35Cl** and **37Cl**, which produce characteristic isotope patterns in chlorine-containing compounds. The workflow evaluates the agreement between the observed M, M+2, and M+4 isotope intensity ratios and the theoretical isotopic distributions expected from the natural abundances of chlorine isotopes.

The approximate mass differences evaluated are:

```text
M → M+2 = 1.99705 Da
M → M+4 = 3.99410 Da
```

The MS1 workflow also evaluates signals in procedural controls and filter blanks. Candidate isotope patterns are retained only when they satisfy the specified isotope-pattern and background criteria.

Code for this analysis is located in:

```text
Rule 1/
```

### Rule 2: HCl Neutral Loss

Rule 2 searches MS/MS spectra for fragment relationships consistent with neutral loss of HCl.

Detection of an HCl neutral loss provides supporting evidence for a chlorine-containing precursor or fragment.

Code for this analysis is located in:

```text
Rule 2/
```

### Rule 3: Cl Radical Loss

Rule 3 searches MS/MS spectra for fragmentation relationships consistent with loss of a chlorine radical.

This provides an additional MS/MS-based source of evidence for chlorine-containing compounds.

Code for this analysis is located in:

```text
Rule 3/
```

### Rule 4: Chlorine-Containing Fragment Pairs

Rule 4 evaluates MS/MS spectra for fragment pairs consistent with chlorine-containing fragmentation patterns.

This rule provides an additional and independent MS/MS evidence stream that can be used to support candidate prioritization.

Code for this analysis is located in:

```text
Rule 4/
```

---

## 5. Integrated ChloroFinder and MS1 Prioritization

The integrated prioritization workflow combines the ChloroFinder machine learning probability with MS1 chlorine isotope evidence.

The current combined score is:

```text
CombinedScore = 0.60 × ChloroFinder probability + 0.40 × MS1 isotope score
```

Candidate features are considered CombinedScore-positive when:

```text
CombinedScore ≥ 0.60
```

The two components provide complementary information:

- **ChloroFinder probability** represents evidence learned from MS/MS fragmentation patterns.
- **MS1 isotope score** represents direct evidence from the characteristic chlorine isotope pattern.

Rule 4 is retained as an independent source of supporting MS/MS evidence but does not contribute directly to the CombinedScore.

The integrated workflow is located in:

```text
Option B Code/
```

---

## 6. Background Correction

MS1 isotope evidence is evaluated against procedural controls and filter blanks to reduce false-positive assignments caused by background ions.

The workflow includes:

```text
Control_Chloro
Control_Hydrolysis
Filter_Blank
```

For a candidate feature to retain MS1 chlorine isotope evidence, the sample signal must satisfy the specified intensity and sample-to-background criteria in addition to matching the expected chlorine isotope pattern.

This step helps distinguish sample-associated chlorine isotope evidence from signals also present in controls or analytical backgrounds.

---

## 7. Structural Annotation

ChloroFinder is designed to complement conventional nontarget screening and structural annotation rather than replace them.

The repository contains workflows associated with structural annotation using complementary computational approaches.

### patRoon

The patRoon workflow is provided in:

```text
RSCRIPT_FOR_PATROON.ipynb
```

The workflow includes:

- OpenMS feature processing
- MS/MS peak-list generation
- GenForm molecular formula generation
- MetFrag candidate structure annotation
- PubChem candidate searching

The resulting molecular formulas and candidate structures can be evaluated alongside chlorine-specific evidence generated by ChloroFinder.

Structural annotations can therefore be prioritized when they are independently supported by chlorine isotope patterns, diagnostic fragmentation evidence, or ChloroFinder predictions.

---

## 8. Experimental Demonstration

The framework was demonstrated using chemicals released from three thermoplastic polyurethane materials followed by chlorination.

The three TPU chemistries investigated were:

```text
TPU_Ester
TPU_Ether
TPU_Hard
```

The experimental workflow evaluates how chemicals released from different TPU materials are subsequently transformed during chlorination and uses complementary analytical and computational evidence to identify and prioritize chlorinated transformation products.

Experimental conditions were intentionally selected to accelerate polymer degradation and transformation and generate sufficient reaction products for comprehensive LC-HRMS characterization. Consequently, the polymer loading, chlorine concentration, and incubation period should not be interpreted as representative of typical environmental or drinking-water treatment conditions.

---

## 9. Example mzML Data

Example LC-HRMS/MS data are provided in:

```text
mzML_Files/
```

These files can be used to test portions of the ChloroFinder workflow, including MS1 isotope evaluation and integration of mass spectrometric evidence.

---

## 10. Software Requirements

The Python workflows primarily require:

```text
Python 3.x
NumPy
pandas
SciPy
scikit-learn
joblib
Matplotlib
Pyteomics
psims
```

The main Python dependencies can be installed using:

```bash
pip install numpy pandas scipy scikit-learn joblib matplotlib pyteomics psims
```

Some workflows were developed and executed using Google Colab.

Additional software used in the broader nontarget screening workflow includes:

```text
MZmine
SIRIUS
ZODIAC
CSI:FingerID
CANOPUS
patRoon
OpenMS
GenForm
MetFrag
```

---

## 11. Running ChloroFinder

### Step 1: Clone the repository

```bash
git clone https://github.com/jwasswa2023/ChloroFinder.git
cd ChloroFinder
```

### Step 2: Load the trained model

```python
import joblib

bundle = joblib.load("chlorofinder_massbank.pkl")

model = bundle["model"]
mlb_frag = bundle["mlb_frag"]
mlb_delta = bundle["mlb_delta"]
ROUND_DP = bundle.get("round_dp", 3)
```

### Step 3: Prepare experimental MS/MS spectra

Experimental spectra should be converted into the feature-table format required by the ChloroFinder application scripts.

The table should contain precursor information, retention time, source file information, and MS/MS fragment m/z values.

### Step 4: Apply ChloroFinder

Use the scripts provided in:

```text
USING CHLOROFINDER/
```

to calculate ChloroFinder probabilities for experimental MS/MS spectra.

### Step 5: Evaluate chlorine-specific evidence

Apply the complementary chlorine-specific workflows provided in:

```text
Rule 1/
Rule 2/
Rule 3/
Rule 4/
```

### Step 6: Integrate ML and MS1 evidence

Use the scripts in:

```text
Option B Code/
```

to calculate:

```text
CombinedScore = 0.60 × ML + 0.40 × MS1
```

and generate prioritized candidate tables.

---

## 12. Main Outputs

Depending on the analysis performed, the ChloroFinder workflow generates:

- ChloroFinder probabilities
- MS1 chlorine isotope scores
- predicted number of chlorine atoms from isotope patterns
- HCl neutral-loss evidence
- Cl radical-loss evidence
- chlorine-containing fragment-pair evidence
- integrated CombinedScore
- evidence-based prioritization tiers
- prioritized candidate feature tables
- diagnostic summaries and figures

These outputs are intended to support candidate prioritization and interpretation rather than provide definitive structural identification.

---

## 13. Interpretation of ChloroFinder Predictions

A ChloroFinder probability represents the degree to which an experimental MS/MS spectrum resembles fragmentation patterns learned from chlorinated compounds in the reference training dataset.

A high ChloroFinder probability should therefore **not be interpreted as definitive confirmation that a compound contains chlorine or as a definitive structural identification**.

Confidence increases when ChloroFinder predictions are supported by independent evidence, including:

- characteristic chlorine isotope patterns,
- HCl neutral loss,
- Cl radical loss,
- chlorine-containing fragment pairs,
- chlorine-containing molecular formulas,
- structural annotations,
- spectral-library matches.

ChloroFinder is therefore intended as an **evidence-integration and prioritization framework** for nontarget LC-HRMS/MS analysis.

---

## 14. Reproducibility

For reproducible application of ChloroFinder:

1. Use the provided trained model and associated feature encoders.
2. Process fragment ions using the same m/z precision used during model development.
3. Calculate pairwise Δm/z features using the same preprocessing procedure.
4. Retain the continuous ChloroFinder probability.
5. Evaluate MS1 isotope evidence using the specified mass and isotope-ratio tolerances.
6. Apply the specified background criteria to MS1 evidence.
7. Retain individual evidence streams in addition to the integrated score.
8. Record all thresholds and processing parameters used for candidate prioritization.

Intermediate results should be retained so that the contribution of each evidence source can be independently inspected.

---

## Citation

If you use ChloroFinder or the associated workflows, please cite the corresponding publication:

```text
Wasswa, J. et al.
Integrating Nontargeted Screening and Machine Learning for Enhanced
Annotation and Prioritization of Plastic Degradation Products:
A Demonstration Using Chlorinated Thermoplastic Polyurethanes.
Manuscript in preparation.
```

The complete journal citation and DOI will be added following publication.

---

## Contact

**Joseph Wasswa, Ph.D.**  
Environmental Chemistry & Chemoinformatics Lab  
SUNY Polytechnic Institute  
Utica, New York, USA  

Email: wasswaj@sunypoly.edu

For questions about the code, model, or analytical workflow, please open an issue in this repository or contact the corresponding author.


---

## License

This project is licensed under the **MIT License**. See the [LICENSE](https://github.com/jwasswa2023/ChloroFinder/blob/main/LICENSE) file for details.

---

## Acknowledgments

Artificial intelligence tools from **OpenAI, Anthropic, and Google** were used as coding aids during development.


