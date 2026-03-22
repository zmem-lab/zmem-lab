# CRC Validation Notebook

This directory contains the Jupyter notebook that reproduces the scientific validations of the **Cosmic Rhythm Consistency (CRC)** framework.

---

## 📓 `CRC_Framework_Validation.ipynb`

The notebook implements the full validation pipeline described in the CRC abstract (Sá Vieira, A. de 2026) and associated paper:

### Section 1 — Environment Setup

Installs all required packages and imports scientific libraries. Cell 1 need only be executed once per fresh runtime (Colab or local Jupyter).

### Section 2 — Survey Selection and Instrument Parameters

Loads official 2026 instrument parameters for the selected survey. All values are taken directly from peer-reviewed references.

| Survey | Instrument | τ_dwell |
|---|---|---|
| `BINGO` | BAO from Integrated Neutral Gas Observations (21 cm drift-scan) | 166 s |
| `SIMONS` | Simons Observatory Small Aperture Telescope (constant-elevation scan) | 2 s |
| `LSST` | Vera C. Rubin Observatory — LSST wide-field survey visits | 40 s |

To switch instrument, modify only the `SURVEY` variable in Cell 3.

### Section 3 — Synthetic Sky Simulation

Generates a mock cosmological HEALPix sky map using a lognormal HI brightness temperature model consistent with Motta et al. (2026), then constructs a Time-Ordered Data (TOD) stream by scanning the map at the instrument's sidereal drift speed. Synthetic impulsive transients are injected into the TOD for controlled validation.

### Section 4 — Cosmic Rhythm Consistency Algorithms

Implements the two-stage CRC pipeline described in the associated paper:

- **Algorithm 1 — Event Detection**: identifies transient candidates in the TOD via a running-median/MAD adaptive threshold. Complexity:

$$O(N\ \log\ N) \text{ (vectorised implementation)}$$

- **Algorithm 2 — Temporal Persistence Filter**: rejects candidates whose duration satisfies

$$\Delta t < \tau_{\text{dwell}}$$

and generates cryptographically verifiable Instrumental Event Provenance (IEP) tags at two levels of detail (SHA-256 hashing; ED25519 signature placeholder).

### Section 5 — Output, Diagnostics and Manuscript Summary

Serialises IEP tags and validation statistics to disk (JSON), verifies the pipeline state by inspecting all required variables, and prints a formatted summary suitable for inclusion as supplementary material in a journal submission.

### Section 6 — Visualisation

Produces publication-quality figures illustrating the filter behaviour:

- **Figure A** — Time-Ordered Data with flagged and rejected transient events
- **Figure B** — Logarithmic comparison of dwell/duration times across interference source classes, with the rejection boundary

$$\tau_{\text{dwell}}$$

highlighted.

### Section 7 — Stage-IV Validation (Optional)

The notebook includes a graceful fallback mechanism: if a high-fidelity sky map is present in the runtime environment, it is automatically loaded and processed; otherwise, the pipeline proceeds with the synthetic lognormal map from Section 3, which is fully sufficient for algorithm validation and manuscript submission.

To enable validation against Stage-IV-grade simulations:

1. Obtain a HEALPix-format sky map (`Nside=256`) from a trusted source:
   - **LSST Dark Energy Science Collaboration** simulated products [[1]](https://lsstdesc.org)
   - **Uchuu** N-body simulation archive [[2]](https://uchuu.dev)
   - **HalfDome** project documentation for methodological reference [[3]](https://halfdomesims.github.io)
2. Pre-process the map to match the notebook's expected format:
   - Filename: `stage4_y_map_nside256.fits` (or customise the loader in Cell 12)
   - Units: dimensionless Compton-*y* parameter or brightness temperature, as appropriate
3. Place the file in the runtime directory:
   - **Colab**: Upload to `/content/` via the Files panel
   - **Local Jupyter**: Place in the same directory as the notebook
4. Re-execute Cells 4–11 to propagate the new map through the full CRC pipeline.

> ℹ️ *Note: The synthetic lognormal sky generated in Section 3 has been validated to reproduce the statistical properties required for CRC algorithm testing (power-law C_ℓ, lognormal HI fluctuations, beam smoothing). For most validation purposes, including manuscript submission and peer review, the synthetic map is adequate.*

$$C_\ell$$

**Citation (if using external simulations):**

If you extend this work using external simulation products, please cite the corresponding data release (e.g. Ishiyama et al. 2021, MNRAS, 506, 4210).

### Section 8 — Empirical Validation: CHIME/FRB First Catalogue

Validates the CRC framework against real astrophysical transient data drawn from the CHIME/FRB Collaboration (2021), ApJS 257, 59 ([DOI: 10.3847/1538-4365/ac33ab](https://doi.org/10.3847/1538-4365/ac33ab)).

**Validation rationale:** Fast Radio Bursts exhibit intrinsic durations

$$\Delta t_{\mathrm{FRB}} \sim 0.5\text{–}20 \text{ ms}$$

which satisfy

$$\Delta t_{\mathrm{FRB}} \ll \tau_{\text{dwell}} \approx 166 \text{ s (BINGO frame)}$$

by approximately four orders of magnitude. The CRC temporal persistence filter therefore correctly classifies every FRB as an impulsive transient — the intended behaviour, since FRBs are not the target 21 cm cosmological signal. Post-hoc scientific recovery of rejected FRB events is enabled through IEP cross-matching with public transient catalogues (CHIME/FRB, ASKAP).

**Data sources**: [CHIME/FRB Catalogue](https://www.chime-frb.ca/catalog) | [Data Dictionary](https://www.chime-frb.ca/information)

**Expected outcome:** 100% rejection efficiency across the full catalogue sample.

---

## 🚀 Execution Guide

### Option 1: Google Colab (Recommended for Quick Testing)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/zmem-lab/crc/blob/main/notebooks/CRC_Framework_Validation.ipynb)

Simply click the badge and execute cells sequentially. No local installation required. All dependencies are installed automatically by Cell 1.

### Option 2: Local Jupyter (For Development and Customisation)

[![Jupyter Notebook](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter)](https://github.com/zmem-lab/crc/blob/main/notebooks/CRC_Framework_Validation.ipynb)

```bash
# Clone the repository
git clone https://github.com/zmem-lab/crc.git
cd crc/notebooks

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter
jupyter notebook CRC_Framework_Validation.ipynb
```

### Software Dependencies

| Library | Minimum Version |
|---|---|
| `healpy` | >= 1.16.0 |
| `numpy` | >= 1.24.0 |
| `scipy` | >= 1.10.0 |
| `matplotlib` | >= 3.7.0 |
| `astropy` | >= 5.2.0 |
| `jupyter` | >= 1.0.0 |
| `pandas` | >= 2.0.0 |

---

*ZMEM Research Initiative | [GitHub](https://github.com/zmem-lab/crc)*

