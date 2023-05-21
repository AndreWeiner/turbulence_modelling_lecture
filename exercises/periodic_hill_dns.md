![CC](https://i.creativecommons.org/l/by/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).

# Flow over periodic hills - DNS

The *periodic hills* test case is a popular choice for benchmarking turbulence models since it combines several challenging flow configurations like positive/negative pressure gradients, recirculation, separation, and reattachment. The test case is relatively simple to set up but poses a severe challenge for most turbulence models. Therefore, DNS data of this configuration is extremely valuable for improving or tuning turbulence models. Performing a full DNS on a simple workstation would be infeasible. However, in this exercise we go through the potential steps for creating new DNS reference data. Specifically, we:

- inspect the DNS reference data at $Re=2800$
- compare a coarse-grid simulation to the reference
- perform an efficient mesh dependency study

## Test case description and reference DNS

A detailed description of the test case and the reference DNS can be found in [P. Balakumar (2015)](https://arc.aiaa.org/doi/10.2514/6.2015-2783):
> DNS/LES simulations of separated flows at high Reynolds numbers

A shorter description and the main results are also available in the [Nasa turbulence modeling resources](https://turbmodels.larc.nasa.gov/Other_DNS_Data/2dhill_periodic_compress.html). The Reynolds number based on the average inlet velocity $U_b$ and the hill height $h$ is $Re=2800$. The author uses a compressible flow solver, but the flow is only weekly compressible ($Ma=0.2$). The grid resolution is $801\times 351\times 513$ in streamwise, normal, and spanwise direction, respectively. The total cell count is roughly $144\times 10^6$.

The author of the study provides several flow quantities extracted from the DNS. We will only use the time-averaged velocity profiles and the Reynolds stresses at several $x$ stations, but it also worth it to take a look at the velocity fields depicted on the NASA website linked above. Make sure to download the [profiles.dat](https://turbmodels.larc.nasa.gov/Other_DNS_Data/2Dhill_periodic_compress/profiles.dat) file, which contains the average velocity profiles and Reynolds stresses.

## OpenFOAM test case

The OpenFOAM setup is based on [this LES simulation](https://www.openfoam.com/documentation/guides/latest/doc/verification-validation-turbulent-periodic-hill.html) from the tutorial collection. In contrast to the reference DNS, the test case located under *test_cases/periodic_hills_base* has the following modifications:

- the flow is treated incompressible (*pimpleFoam* solver)
- the spanwise extend of the domain is only $2.25h$ rather than $4.5h$
- the resolution is only $100\times 80\times 20$ yielding a total of $160\times 10^3$ cells

Note that this setup is not meant to reproduce the quality of the reference DNS. Our aim is to set up a similar simulation and to extract similar quantities of interest. The following list provides an overview of the most important elements of this simulation:

- **meanVelocityForce:** the *system/fvOption* file defines an additional source term for the momentum equation; this force makes sure that the mean inlet velocity remains $U_b = 1.0m/s$ (the boundaries at $x=0h$ and $x=9h$ are cyclic); to visualize the cell zone in which the force is applied, open ParaView, check the box *Read zones* in the properties panel before clicking on apply, and then create an *extractBlock* filter to isolate the *inletCellZone*
- **fieldAverage FO:** this function object computes the temporal mean of a field as well as the temporal mean of the fluctuations, e.g., $\overline{u_i^\prime u_j^\prime}$ for the velocity vector
- **columnAverage FO:** even though the flow is statistically 2D, we would need to sample over a long time before the mean in spanwise direction approaches zero; to remove some of the variance in spanwise direction, this function object computes the arithmetic mean in $z$-direction; note that this FO works only for extruded meshes
- **sampling FO:** this function object samples the spanwise averaged temporal mean/variance of the velocity vector at several $x$-positions in order to compare these profiles with the reference DNS

To run the base simulations, execute the following steps:

- create a dedicated directory in your run folder, e.g., *periodic_hill_dns*
- create a copy of *test_cases/periodic_hills_base* in the newly created folder
- if necessary, adjust the parallelization (8 subdomains by default)
- run the simulation by executing the *Allrun* script; the computation takes around 2h to complete

In the meantime, we can implement the data analysis script. The following steps may guide the process:

- take a close look at the *controlDict* and, in particular, at the function objects
- create a new script or Jupyter notebook that parses and plots the reference DNS data (*profiles.dat*); note that the file format is rather particular and requires manual parsing (rather than resorting to Pandas' *read_csv()*); as a starting point, load the file and create a list containing all lines, e.g., `ref_data = open("profiles.dat").read().splitlines()`
- the line samples written by the OpenFOAM simulation are straightforward to parse using *read_csv()*; there are 10 columns in each samples file, 1 for the $y$-coordinate, 3 for the mean velocity vector, and 6 for the Reynolds stresses; if you are unsure about the ordering of the stresses, refer to the *components* of [symmetricTensor](https://www.openfoam.com/documentation/guides/latest/api/classFoam_1_1SymmTensor.html); once the simulation is complete, load the data from the latest write time, i.e., *5*, and plot the profiles of mean velocity and Reynolds stresses against the reference data; if you do not have the computational resources to complete the simulation, you can also download the line samples [here](https://cloud.tu-braunschweig.de/s/krYCJ9ffypjDGjt)

## Effective mesh dependency study

Of course, the coarse grid simulation is far from the quality of the reference DNS. However, the solution computed on the coarse grid is still a significantly better starting point than a uniform velocity/pressure distribution. Therefore, we can set up a new simulation with refined mesh and map the latest coarse grid solution to the fine mesh. The following steps may guide you:

- create another copy of the *test_cases/periodic_hills_base* simulation in the *periodic_hills_dns* folder and name it *periodic_hills_ref*
- open the *system/blockMeshDict* and double the spatial resolution by replacing *(100 80 20)* with *(200 160 40)* in the *blocks* section
- in the *system/controlDict*, halve the numerical time step and set the *timeStart* to 0.0 for all function objects; since we start from a much more physical initial state, we can also start the time-averaging right from the beginning
- in the *Allrun* script, include the line `runApplication mapFields ...` after the domain decomposition; you still need to complete the command; inspect the output of `mapFields -help` to infer suitable options
- start the simulation; you do not have to run this simulation until completion, but make sure that the setup work correctly and that the coarse grid solution was mapped successfully (e.g., by inspecting the output of *log.mapFields*)
- in case you have enough resources to run the refined simulation until completion, compare the results to those computed on the coarse grid