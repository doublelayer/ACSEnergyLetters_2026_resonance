# Data for “Controlling water–hydroxyl resonance on dual-atom site catalysts”

## Authors and contact

Ba Long Nguyen¹; Emmanouil Pervolarakis²; Marcus Frahm Nygaard²; Martin Lillebro Lundquist²; Vladislav Ivanistsev³; Nadezda Kongi¹; Jan Rossmeisl².

1. Institute of Chemistry, University of Tartu, Ravila 14a, 50411 Tartu, Estonia.
2. Center for High Entropy Alloy Catalysis, Department of Chemistry, University of Copenhagen, Universitetsparken 5, 2100 Copenhagen, Denmark.
3. Department of Chemistry, University of Latvia, Jelgavas iela 1, LV-1004 Riga, Latvia.

Correspondence: vladislav.ivanistsev@lu.lv; nadezda.kongi@ut.ee; jan.rossmeisl@chem.ku.dk.


| Author | ORCID |
| --- | --- |
| Ba Long Nguyen | https://orcid.org/0009-0003-7682-9851 |
| Emmanouil Pervolarakis | https://orcid.org/0000-0001-7813-9553 |
| Marcus Frahm Nygaard | https://orcid.org/0000-0002-8382-4632 |
| Martin Lillebro Lundquist | https://orcid.org/0000-0003-4209-2196 |
| Vladislav Ivanistsev | https://orcid.org/0000-0003-4517-0540 |
| Nadezda Kongi | https://orcid.org/0000-0001-9680-0421 |
| Jan Rossmeisl | https://orcid.org/0000-0001-7749-6567 |

## Contents

Optimized molecular dual-atom site catalysts (DASCs) containing Cr–Cu metals, B1/B2 base fragments, N/O/S bridges, and A1/A2/A4/A5/A6/A9 acid fragments.
The manuscript reports 1,659 intact OH–H₂O co-adsorption structures and 154 single-water reference structures, together with available corresponding bare catalysts.

| Filename stem (each supplied as `.db` and `.json`) | Contents |
| --- | --- |
| `DASC_OH_H2O_coadsorption_RPBE_D4` | co-adsorption geometries, interaction-energy components, filtering metadata, and available Bader charges and atomic magnetic moments |
| `DASC_H2O_Adsorption_RPBE_D4` | Single-water reference geometries and filtering metadata |
| `DASC_Bare_Catalysts_RPBE_D4` | Independently optimized bare-catalyst geometry references and filtering metadata |

The `.db` (ASE SQLite) and `.json` (ASE JSON) files are equivalent alternatives; do not combine them as separate observations.
All four interaction-energy components are stored in the co-adsorption database and use the co-adsorption geometry or frozen fragments obtained from it.
In particular, `sp_rpbe_d4_E_bare_cat` is a frozen fragment energy, not an energy taken from the separate bare-catalyst database.
The bare-catalyst database is used only for geometry references in this analysis, including metal–metal distances.
See `DATA_DICTIONARY.md` for fields, units, and atom indexing.
The release also includes `resonance_analysis.ipynb` for analysis and figure generation, plus `requirements.txt` for dependencies.
DFT wavefunctions are not included.

## Read and filter

Install ASE with `python -m pip install -r requirements.txt`; additional notebook dependencies are listed below.

```python
from ase.db import connect

db = connect("DASC_OH_H2O_coadsorption_RPBE_D4.db")  # Or .json
print(db.count())
for row in db.select(acid="A2", metal1="Fe"):
    atoms = row.toatoms()
    print(row.name, row.total_charge)
```

Use `name` as the record identifier and `catalyst_name` to link databases.
Numeric row IDs can change during conversion.
Obtain exact file counts with `db.count()`; no fixed count is required by the format.

## Run the analysis notebook

Place `resonance_analysis.ipynb` beside the three `DASC_*.db` files.
Install the notebook dependencies and start JupyterLab:

```bash
python -m pip install -r requirements.txt
python -m pip install numpy pandas scipy scikit-learn matplotlib jupyterlab ipython
jupyter lab resonance_analysis.ipynb
```

Check that the setup cell uses the three DASC filenames listed above and sets `DATA_DIR` to their directory.
Restart the kernel and run all cells in order.
To use the JSON copies, change the three input extensions from `.db` to `.json`.
No new GPAW calculation is required.

The notebook generates Figures 2–4 and SI2–SI7 in `analysis_outputs/`.
Figure 1 (representative structures) and Figure SI1 (workflow) are prepared separately.
The analysis covers geometric classification using Gaussian mixture models, interaction energies and non-additive stabilization, family-cluster bootstrap uncertainty, metal, bridge, and para-substituent effects, water structural and electronic descriptors, and comparisons with matched single-water and bare-catalyst reference structures.
ASE is pinned to 3.28.0.

## Methods and provenance

Spin-polarized RPBE-D4 calculations used GPAW 24.6 and dftd4 3.6.0 through ASE SumCalculator: plane-wave cutoff 550 eV, grid spacing 0.15 Å, at least 6.00 Å vacuum per side, and maximum optimization force below 0.05 eV Å⁻¹.
Preoptimization used GFN2-xTB or UMA-S-1p2; initial magnetic states were screened when needed.

The co-adsorption export combines geometry, scalar energy components, and available standard calculator results from the single-point source with Bader charges and custom atomic magnetic moments from matching merged records.
Geometry and atom ordering were checked before joining these records.
Reference structures and available standard calculator results come from the corresponding single-water and bare-catalyst sources.
Filtering metadata were retained or recovered from matching catalyst records.

Only intact co-adsorption structures selected for analysis are included; excluded proton-transferred candidates are not supplied.
Unavailable bare references are omitted, so not every co-adsorption record has a bare reference.
Missing charge, moment, force, or other properties mean unavailable data, not zero.
Standard calculator-result availability varies by record.
The included notebook supplies the analysis procedure for the numerical figures listed above.

## Citation, access, and reuse

Cite this dataset using the final Zenodo record citation and version DOI, and cite the related manuscript when available.
The manuscript title and authors are listed above; no publication DOI is asserted here.
The data and documentation are licensed under Creative Commons Attribution 4.0 International (CC BY 4.0): https://creativecommons.org/licenses/by/4.0/.
The related paper DOI is not yet available and will be added after publication.
