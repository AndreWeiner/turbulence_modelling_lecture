# Modelling and simulation of turbulent flows

This repository contains resources accompanying the lecture **fundamentals of turbulence modeling** provided by the Institute of Fluid Mechanics at TU Braunschweig. **Note that this repository as well as the linked slides will be updated regularly throughout the summer term 2023**.

## Syllabus

The course consists of a series of lectures and accompanying practical exercises. The exercises focus on the setup and evaluation of turbulent flow simulations. Basic knowledge about the finite volume method, computational fluid mechanics, and programming are assumed. In terms of software, the exercises employ OpenFOAM for the simulations and Python/Jupyter notebooks for the data analysis. No proprietary software licenses are needed.

### Lecture

| #   | topic | slides |
|:---:|:------|:------:|
| 1 | **A brief overview of turbulence modelling:** course overview, equations of fluid motion, basic definitions, history of turbulence modelling| [Link](https://andreweiner.github.io/turb-slides/intro.html) |
| 2 | **The finite volume method:** solution of transport equations with the finite volume method | [Link](https://andreweiner.github.io/turb-slides/fvm.html) |
| 3 | **Mean-flow equations:** Reynolds-averaged equations and stresses, turbulence kinetic energy | [Link](https://andreweiner.github.io/turb-slides/rans_0.html) |
| 4 | **Canonical turbulent flows:** jet, mixing layer, wake, flat plate, pipe | [Link](https://andreweiner.github.io/turb-slides/canonical.html) |
| 5 | **Direct numerical simulations:** numerical techniques, resolution requirements, selected results | [Link](https://andreweiner.github.io/turb-slides/dns.html) |
| 6 | **RANS I:** eddy viscosity hypothesis, mixing length models | [Link](https://andreweiner.github.io/turb-slides/rans_1.html) |
| 7 | **RANS II:** one- and two-equation models | [Link](https://andreweiner.github.io/turb-slides/rans_2.html) |
| 8 | **RANS III:** modeling of anisotropic effects | [Link](https://andreweiner.github.io/turb-slides/rans_3.html) |
| 9 | **LES I:** filtered equations, resolution requirements | [Link](https://andreweiner.github.io/turb-slides/les.html) |
| 10 | **LES II:** filters and subgrid-scale models | [Link](https://andreweiner.github.io/turb-slides/les.html) |
| 11 | **LES III:** hybrid-RANS-LES methods | [Link](https://andreweiner.github.io/turb-slides/hybrid.html) |
| 12 | **Compressible flows:** Favre-averaging | [Link](https://andreweiner.github.io/turb-slides/compressible.html) |
| 13 | **Data-driven turbulence modelling:** supervised learning, field inversion, reinforcement learning| [Link](https://andreweiner.github.io/turb-slides/data.html) |

### Exercise

| #   | topic | instructions |
|:---:|:------|:------:|
| 1 | **Setting up your system:** installing software dependencies | [Link](exercises/system_setup.md) |
| 2 | **OpenFOAM basics I:** simulation case structure and solvers | [Link](exercises/of_basics.md) |
| 3 | **OpenFOAM basics II:** post-processing | [Link](exercises/of_basics.md) |
| 4 | **Forced isotropic turbulence in a box:** case setup, evaluation of the turbulent spectrum | [Link](exercises/hit.md) |
| 5 | **Flow over periodic hills:** resolution requirements, computational cost, post-processing (Q-criterion, cutting planes, sampling lines, statistics) | [Link](exercises/periodic_hill_dns.md) |
| 6 | **Flow over a 2D airfoil:** resolution requirements, evaluation of surface pressure and wall shear stress | [Link](exercises/airfoil_2d.md) |
| 7 | **Flow over a rearward facing step:** resolution requirements, setup and evaluation of linear two-equation models| [Link](exercises/backward_facing_step.md) |
| 8 | **Flow over a rearward facing step:** setup and evaluation of non-linear two-equation and Reynolds-stress models | [Link](exercises/backward_facing_step.md) |
| 9 | **Re-visiting the flow over periodic hills I:** resolution requirements, LES setup, comparison to DNS | [Link](exercises/periodic_hill_les.md) |
| 10 | **Re-visiting the flow over periodic hills II:** evaluation of turbulent length scales | [Link](exercises/periodic_hill_les.md) |
| 11 | **Hydrofoil with trailing edge separation:** resolution requirements, IDDES setup | [Link](exercises/hydrofoil.md) |
| 12 | **Hydrofoil with trailing edge separation:** comparison to experimental results | [Link](exercises/hydrofoil.md) |
| 13 | **Inverting Spalding's function:** creating a machine learning model of the inverted Spalding's function| [Link](exercises/spalding_ml.md) |

## Abbreviations

- **AZDES:** automated ZDES
- **CFD:** computational fluid dynamics
- **DDES:** delayed DES
- **DES:** detached eddy simulation
- **DNS:** direct numerical simulation
- **FVM:** finite volume method
- **IDDES:** improved DDES
- **LES:** large eddy simulation
- **RANS:** Reynolds-averaged Navier-Stokes
- **ZDES:** zonal DES

## References

### Scientific articles

### Books

- *An introduction to computational fluid dynamics: the finite volume method* by H. K. Versteeg and W. Malalasekera
- *Large eddy simulation for incompressible flows* by P. Sagaut
- *Large Eddy Simulation turbulenter Strömungen* by J. Fröhlich
- *The finite volume method in computational fluid dynamics* by F. Moukalled, L. Mangani, and M. Darwish
- [The OpenFOAM technology primer](https://zenodo.org/record/4630596#.YXBgepuxVH4) by T. Marić, J. Höpken, and K. G. Mooney
- *Turbulent flows* by S. B. Pope
- *Turbulence modeling for CFD* by D. W. Wilcox