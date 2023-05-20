![CC](https://i.creativecommons.org/l/by/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).

# Forced isotropic turbulence in a box

In this exercise, we simulate one of the most fundamental canonical turbulent flows, namely decaying isotropic turbulence in a box. In particular we:

- create a random velocity field according to the energy spectrum coming from experiments
- evolve the initial velocity field in time and compute the energy spectrum at fixed time intervals
- compare Energy spectrum's evolution between experiment and simulation
- investigate the influence of mesh resolution and turbulence modelling

## Experimental reference data

The reference data comes from the experiments reported in [G. Comte-Bellot, S. Corrsin (1971)](https://doi.org/10.1017/S0022112071001599):
> Simple Eulerian time correlation of full- and narrow-band velocity signals in grid-generated isotropic turbulence

In the experiments, a grid with openings of $M = 5.08 cm$ ($2in$) is placed in a channel flow with an average inlet velocity of $\overline{U} = 10m/s$. The kinematic viscosity of air at room temperature is about $\nu =1.5\times 10^{-5} m^2/s$. The resulting Reynolds number is $Re=\overline{U}M/\nu = 34000$.

Taylor postulated that the contribution of turbulent fluctuations to the advection of flow structures is small compared to the advection resulting from the mean flow (in homogeneous, statistically stationary flow). Based on his hypothesis, we can relate temporal to spatial fluctuations by means of the mean velocity $\overline{u}$:

$$
  \partial_t (\dots) = \overline{u}\partial_x (\dots)
$$

The authors of the study mentioned above report the velocity fluctuations measured by several hot wires along the channel in terms of an energy spectrum (a discrete Fourier transformation). Relying on Taylor's hypothesis, the authors connect positions of the sensors downstream the grid to the time a parcel of *frozen turbulence* has traveled along the channel:

$$
  t - t_0 = (x-x_0) / \overline{U}
$$

and report the spectra at dimensionless times of $\tilde{t} = t\overline{U}/M = \{42, 98, 171\}$.

The code snippet below contains the data extracted from table 3 of the reference and demonstrates the typical double-logarithmic visualization of these spectra.

```Python
from os.path import join
import matplotlib.pyplot as plt
from pandas import read_csv
import numpy as np

plt.rcParams["figure.dpi"] = 160

k_42 = np.asarray((0.2, 0.25, 0.3, 0.4, 0.5, 0.7, 1.0, 1.5, 2.0, 2.5, 3.0, 4.0, 6.0, 8.0, 10.0, 12.5, 15.0, 17.5, 20.0)) * 100
E_42 = np.asarray((1.29e2, 2.3e2, 3.22e2, 4.35e2, 4.57e2, 3.8e2, 2.7e2, 1.68e2, 1.2e2, 8.9e1, 7.03e1, 4.7e1, 2.47e1, 1.26e1, 7.42, 3.96, 2.33, 1.34, 8.0e-1)) * 1.0e-6
k_98 = k_42
E_98 = np.asarray((1.06e2, 1.96e2, 1.95e2, 2.02e2, 1.68e2, 1.27e2, 7.92e1, 4.78e1, 3.46e1, 2.86e1, 2.31e1, 1.43e1, 5.95, 2.23, 9.0e-1, 3.63e-1, 1.62e-1, 6.6e-2, 3.3e-2)) * 1e-6
k_171 = np.asarray((0.15, 0.2, 0.25, 0.3, 0.4, 0.5, 0.7, 1.0, 1.5, 2.0, 2.5, 3.0, 4.0, 6.0, 8.0, 10.0, 12.5, 15.0)) * 100
E_171 = np.asarray((4.97e1, 9.2e1, 1.2e2, 1.25e2, 9.8e1, 8.15e1, 6.02e1, 3.94e1, 2.41e1, 1.65e1, 1.25e1, 9.12, 5.62, 1.69, 5.2e-1, 1.61e-1, 5.2e-2, 1.41e-2)) * 1e-6

labels = (r"$\tilde{t}=42$", r"$\tilde{t}=98$", r"$\tilde{t}=171$")
colors = ("C0", "C1", "C2")
for k, E, c, l in zip((k_42, k_98, k_171), (E_42, E_98, E_171), colors, labels):
    plt.plot(k, E, c=c, label=l)
plt.xscale("log")
plt.yscale("log")
plt.xlabel(r"$k$ in $1/m$")
plt.ylabel(r"$E$ in $m^3/s^2$")
plt.legend()
plt.show()
```

## Computational setup

The simulation setup located *test_cases/decayIsoTurb_32* aims at reproducing the experimental results by computing the temporal evolution of an initial field of velocity fluctuations in a box (setup based on [this tutorial](https://www.openfoam.com/documentation/guides/latest/doc/verification-validation-turbulent-decay-homogeneous-isotropic-turbulence.html)). If Taylor's hypothesis is applicable, the decay of the spatial velocity fluctuations in the box should be comparable to the temporal fluctuations measures at various positions downstream the grid.

The cubic domain has an edge length of $L=18\pi\ cm$. The *createBoxTurb* utility first creates a mesh and then imposes a velocity field based on a user-defined spectra, which is defined in the dictionary *constant/createBoxTurbDict*. We use the experimental spectrum at $\tilde{t}=42$ as starting point. The process of converting the spectrum into a divergence free velocity field is described in [T. Saad et al. (2016)](https://doi.org/10.2514/1.J055230):
> Scalable tools for generating synthetic isotropic turbulence with arbitrary spectra

Once the velocity field is initialized, the *pimpleFoam* flow solver computes the time evolution and writes snapshots every $0.01s$. The *energySpectrum* function object outputs the energy spectrum into the folder *postProcessing/energySpectrum* at the same write interval.

## Mesh dependency study

Based on the Kolmogorov length and the edge length of the cube, you can estimate the cell size/the number of cells necessary to resolve even the smallest scales. The initial resolution is $32\times 32\times 32$ cells. Considering the estimate based on the Kolmogorov length scale, what do you expect from the quality of the results obtained on the initial grid?

To execute the initial setup, create new folder in the run directory and make a copy of the test case:
```
mkdir $FOAM_RUN/decay
cp -r test_cases/decayIsoTurb_32/ $FOAM_RUN/decay/
cd $FOAM_RUN/decay/decayIsoTurb_32/
```
The setup requires 8 physical CPU cores to run in parallel. In case your PC only has 4 cores, open the *system/decomposeParDict* and adjust the number of subdomains as well the domain decomposition accordingly (e.g., (2 2 1) would be a valid decomposition for 4 subdomains). You can check the number of available physical cores using the command *lscpu* in case you are unsure.

Now you are ready to execute the simulation by running the *Allrun* script. Meanwhile the simulation is running, open a new tab or terminal and create another copy of the original test case, which is named *decayIsoTurb_64*. As the name suggests, we will increase the resolution for this setup. To do so, open the *constant/createBoxTurbDict*, comment out the line with *N (32 32 32);* and remove the comment at the beginning of the line *N (64 64 64);*. Once the first simulation is complete, you can start the refined case. If you have about 16 GB of RAM, you can repeat the same process with a resolution of $128^3$.

By default, the function object does not compute the energy spectrum of time *0* automatically. Therefore, we have to extract the spectrum manually (you may need to adjust the number of processes following *-np*):

```
mpirun -np 8 pimpleFoam -parallel -postProcess -time 0 -func "energySpectrum"
```

Increasing the mesh resolution significantly increases runtime and storage requirements. You can compare the execution times by inspecting the end of the *log.pimpleFoam* file, i.e., `tail -n 30 log.pimpleFoam`. To obtain the cumulative size of the entire simulation, run, e.g,  `du -hs decayIsoTurb_32`.

Finally, we want to compare the energy spectra obtained on the different mesh resolutions with the experimental reference. The following steps guide the process:

- determine the time folders that are closest to the dimensionless times of $\tilde{t}=\{42-42, 98-42, 171-42\}$ based on the definition of $\tilde{t}$
- load the respective spectra using *read_csv()* from the Pandas library (refer to exercise 3 for a refresher if needed)
- plot the numerical against the experimental spectra; create one subplot for each mesh resolution; include also the mesh-specific Nyquist wave number, which is $\pi/\Delta_x$ ($\Delta_x$ is the mesh element size)

## Comparison to large eddy simulation (LES)

Decaying isotropic turbulence is not just a setup to validate Taylor's hypothesis, but it can be also used to validate turbulence models. In the last part of this exercise:

- create another copy with a resolution of $64^3$
- open the *constant/turbulenceProperties* dictionary and change the *simulationType* to LES
- run the simulation and compare the spectra to the experimental reference data as well as the numerical data at the same mesh resolution without turbulence modelling

