![CC](https://i.creativecommons.org/l/by/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).

# Flow over a rearward facing step

In this exercise, we test the $k$-$\omega$-SST turbulence model on the backward facing step benchmark case and compare selected results against experimental reference data.

## Test case description and reference data

The setup is based on the experimental investigations of [D. M. Driver and H. L. Seegmiller (1985)](https://arc.aiaa.org/doi/10.2514/3.8890). Some snapshots of their experiments are available on [this site](https://turbmodels.larc.nasa.gov/backstep_val.html). We compare the skin friction and pressure coefficients on both lower and upper wall as well velocity profiles at several stations along the channel. The backward facing step is another challenging and important test case. The sudden expansion of the channel leads to a recirculation zone behind the step. Capturing the length of this recirculation correctly is of high interest in aerodynamic applications (from the exp. reference article):

> Reattachment of separated turbulent shear layers in the presence of adverse pressure gradients occurs in many aerodynamic flows of interest; for example, flow on airfoils, in diffusers, and in cavities. Extensive separation, with the attendant lack of pressure recovery, leads to a loss in lift and an increase in drag; it is detrimental to the aerodynamic efficiency of a vehicle. In most practical situations, the reattaching shear layer is in the presence of a pressure gradient and occurs on a surface with complex geometry, making strong interaction between the separation and reattachment process likely.

The Reynolds number based on step height $H$, mean inflow velocity $U_{in}$ and kinematic viscosity $\nu$ is $Re=HU_{in}/\nu = 0.0127\cdot44.2/1.56\times10^{-6} \approx 36000$.

## OpenFOAM test case

The OpenFOAM simulation setup is located at *test_cases/backwardFacingStep2D*. The setup is based on [this validation case](https://www.openfoam.com/documentation/guides/latest/doc/verification-validation-turbulent-backward-facing-step.html). Make a copy of the test case in your *run* folder and execute the *Allrun* script. The simulation only takes a few minutes to complete. Once the simulation is done, consider the following tasks:

- use `foamLog log.simpleFoam` to extract the residuals from the log file; visualize the initial residuals for pressure, velocity, and turbulence properties; what do you observe?
- open the solution in ParaView and investigate *yPlus* on the lower and upper walls; inspect also the pressure field of the available snapshots; what do you observe?
- the reference data includes skin friction and pressure coefficients as well as velocity profiles at selected $x$-locations; inspect the function objects located in the *system* folder and make sure to understand how the corresponding data is extracted from the simulation
- compare the simulation results with the reference data; what is the value of the numerically predicted reattachment length?