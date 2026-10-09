# Data dictionary

For “Controlling water–hydroxyl resonance on dual-atom site catalysts.”
All files use the ASE database schema; scalar metadata are key–value pairs and custom arrays are in `row.data`.
Use `name`, not numeric row ID, to identify a structure across formats.

## Filtering metadata

| Key | Definition |
| --- | --- |
| `name` | Unique structure name, including adsorption arrangement |
| `catalyst_name` | Catalyst identifier without the adsorption suffix; links reference records |
| `state` | `OH_H2O`, `H2O`, or `bare`; not the Resonance-On/Off classification |
| `metal1`, `metal2` | Ordered metal element symbols |
| `acid` | Acid-fragment identifier: A1, A2, A4, A5, A6, or A9; see manuscript for chemical construction |
| `base` | B1 or B2 molecular base-fragment identifier; see manuscript for chemical construction |
| `bridge` | NN, NO, NS, OO, OS, or SS bridge composition |
| `total_charge` | Formal net charge in elementary-charge units; positive for cations |
| `ads1`, `ads2` | Coadsorbed species assigned to metal1 and metal2: OH or H2O |
| `n_cat_atoms`, `n_ads1`, `n_ads2` | Atom-count metadata for coadsorption block indexing |
| `h2o_site` | Single-water hosting site, 1 or 2; not a zero-based atom index |

The final four rows apply only to the corresponding adsorption datasets.

## Energy components from the coadsorption geometry (eV)

All four fields below are stored in the coadsorption database.
They refer to the optimized coadsorption structure or frozen fragments obtained by deleting adsorbates from that same structure; no remaining atoms are relaxed.
The `bare_cat` field denotes this frozen fragment, not a record from the separate bare-catalyst database.

| Key | Definition |
| --- | --- |
| `coads_rpbe_d4_E` | Complete optimized coadsorbed complex |
| `sp_rpbe_d4_E_bare_cat` | Catalyst after removing both adsorbates, without relaxation |
| `sp_rpbe_d4_E_only_ads1` | Catalyst plus adsorbate 1, without relaxation |
| `sp_rpbe_d4_E_only_ads2` | Catalyst plus adsorbate 2, without relaxation |

```text
E_int = coads_rpbe_d4_E + sp_rpbe_d4_E_bare_cat
        - sp_rpbe_d4_E_only_ads1 - sp_rpbe_d4_E_only_ads2
```

Negative E_int indicates stabilization under this reference construction.
The single-adsorbate terms include the catalyst; they are not isolated-molecule energies.
The frozen bare-catalyst energy must not be replaced by an energy from `DASC_Bare_Catalysts_RPBE_D4.db` or its JSON counterpart.
That separate database contains independently optimized bare catalysts and is used only as a geometry reference in this analysis, particularly for metal–metal distances.
Any calculator energies retained in the bare-catalyst database are not used in the E_int expression above.

## Atomic data and units

| Field | Definition / unit |
| --- | --- |
| Atomic positions and cell | Å |
| Standard ASE `energy` | eV, where present |
| Standard ASE `forces` | eV Å⁻¹, where present |
| Standard ASE `stress` | eV Å⁻³, where present |
| Standard ASE `magmom`, `magmoms` | μB, where present |
| `row.data['bader_charges']` | Per-atom net charges, e; positive indicates electron depletion |
| `row.data['post_opt_rpbe_d4_magmoms']` | Per-atom signed magnetic moments, μB |

Custom arrays follow the stored atom order.
Bader values are treated as net charges, not integrated electron populations; their sum is checked against `total_charge` in the analysis.
Use the named energy components above for E_int, not a generic ASE energy field.
Missing properties must not be replaced by zero.

## Atom indexing

Indices are zero based.
In the adsorption structures, metal1 and metal2 are atoms 0 and 1.
For coadsorption, adsorbate-block starts are:

```python
cutoff = 6 if acid == "A1" else 0
ads1_start = n_cat_atoms - cutoff
ads2_start = n_cat_atoms + n_ads1 - cutoff
```

Each OH block is O, H; each H₂O block is O, H, H.
The A1 correction accounts for six additional hydrogen atoms appended after the original atom block during transmutation.
Single-water references end with O, H, H.
Do not reorder atoms independently of their arrays and indexing metadata.

## Derived quantities

Water–metal distance is corrected by subtracting the ASE covalent radii of O and the hosting metal.
The shared water H is the water hydrogen nearest the hydroxyl O.
Water charge is the sum of its three atomic charges; hydrogen charge asymmetry is q(shared H) − q(free H).
The reported water magnetic moment is |m(O) + m(H1) + m(H2)|.
Distances use Å without minimum-image wrapping; angles use degrees.
Single-water comparisons match both catalyst identifier and water-hosting site.
Resonance labels, fitted thresholds, group means, and confidence intervals are calculated by `resonance_analysis.ipynb`, rather than stored classifications in the databases.
