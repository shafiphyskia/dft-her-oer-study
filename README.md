# DFT Study of HER and OER

A computational study of **Hydrogen Evolution Reaction (HER)** and **Oxygen Evolution Reaction (OER)** using Density Functional Theory (DFT) implemented in [VASP](https://www.vasp.at/). This repository contains all necessary input files for performing DFT calculations, post-processing scripts, and Jupyter Notebooks for result analysis.

---

## Repository Structure

```
dft-her-oer-study/
├── README.md                    # Overview of the repository
├── HER/                         # Files for HER study (MoS₂)
│   ├── INCAR                    # VASP input settings
│   ├── POSCAR                   # MoS₂ monolayer structure
│   ├── KPOINTS                  # K-Points setup (4×4×1 Gamma mesh)
│   ├── POTCAR                   # Pseudopotentials (Mo + S PAW-PBE)
│   ├── outputs/                 # Directory for VASP results (OUTCAR, CONTCAR, etc.)
│   └── results_analysis.ipynb  # Jupyter Notebook for HER analysis
├── OER/                         # Files for OER study (IrO₂)
│   ├── INCAR                    # VASP input settings
│   ├── POSCAR                   # IrO₂ (110) slab structure
│   ├── KPOINTS                  # K-Points setup (5×5×1 Gamma mesh)
│   ├── POTCAR                   # Pseudopotentials (Ir + O PAW-PBE)
│   ├── outputs/                 # Directory for VASP results
│   └── results_analysis.ipynb  # Jupyter Notebook for OER analysis
├── environment.yml              # Conda environment (Python + ASE + pymatgen)
└── LICENSE                      # MIT License
```

---

## Background

### Hydrogen Evolution Reaction (HER)
The HER is the cathodic half-reaction in water splitting:

```
2H⁺ + 2e⁻ → H₂
```

The key descriptor for HER activity is the **Gibbs free energy of hydrogen adsorption**:

```
ΔG(H*) = ΔE_ads(H*) + ΔZPE - TΔS
```

where:
- `ΔE_ads(H*) = E(slab+H) - E(slab) - ½ E(H₂)` — DFT adsorption energy
- `ΔZPE ≈ +0.04 eV` — zero-point energy correction for H*
- `TΔS ≈ -0.20 eV` at room temperature (300 K)

An ideal HER catalyst has `ΔG(H*) ≈ 0 eV`.

### Oxygen Evolution Reaction (OER)
The OER proceeds through four elementary steps via adsorbed intermediates OH*, O*, and OOH*:

```
H₂O + * → OH* + H⁺ + e⁻        ΔG₁
OH*      → O*  + H⁺ + e⁻        ΔG₂
O* + H₂O → OOH* + H⁺ + e⁻      ΔG₃
OOH*     → O₂ + * + H⁺ + e⁻    ΔG₄
```

The theoretical overpotential is:

```
η = max(ΔG₁, ΔG₂, ΔG₃, ΔG₄) / e − 1.23 V
```

---

## DFT Setup (VASP)

| Parameter         | HER (MoS₂)          | OER (IrO₂)           |
|-------------------|----------------------|----------------------|
| Functional        | GGA-PBE              | GGA-PBE              |
| Pseudopotentials  | PAW-PBE (Mo, S)      | PAW-PBE (Ir_pv, O)   |
| Plane-wave cutoff | 450 eV               | 500 eV               |
| k-points          | 4×4×1 (Gamma)        | 5×5×1 (Gamma)        |
| Smearing          | Gaussian, σ = 0.05   | Gaussian, σ = 0.05   |
| Spin-polarised    | Yes                  | Yes                  |
| Force convergence | 0.02 eV/Å            | 0.02 eV/Å            |
| Energy convergence| 1×10⁻⁵ eV            | 1×10⁻⁵ eV            |
| Ionic relaxation  | IBRION=2, NSW=50     | IBRION=2, NSW=100    |

---

## Getting Started

### 1. Set up the Python environment

```bash
conda env create -f environment.yml
conda activate dft-env
```

### 2. Run VASP calculations

Place a valid `POTCAR` (PAW-PBE) in the corresponding directory and submit:

```bash
cd HER/
mpirun -np 16 vasp_std > vasp.log
```

Copy VASP output files (`OUTCAR`, `CONTCAR`, `DOSCAR`, `vasprun.xml`, etc.) into the `outputs/` subdirectory before running the notebooks.

### 3. Post-process results

```bash
cd HER/
jupyter lab results_analysis.ipynb
```

---

## Requirements

- VASP 5.4+ (licensed)
- Python 3.8+ with the packages listed in `environment.yml`
- Conda (recommended for environment management)

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## References

1. Nørskov, J. K. et al. *J. Electrochem. Soc.* **152**, J23 (2005) — HER volcano plot.
2. Man, I. C. et al. *ChemCatChem* **3**, 1159 (2011) — OER free-energy framework.
3. Kresse, G. & Furthmüller, J. *Phys. Rev. B* **54**, 11169 (1996) — VASP.
4. Perdew, J. P., Burke, K. & Ernzerhof, M. *Phys. Rev. Lett.* **77**, 3865 (1996) — PBE functional.