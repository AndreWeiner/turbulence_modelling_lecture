![CC](https://i.creativecommons.org/l/by/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).

# Flow over a 2D airfoil

In this exercise, we simulate the incompressible, steady flow over a NACA-0012 airfoil and analyze the airfoil's aerodynamic performance as well as typical turbulence modeling quantities.

## Test case description and reference data

A detailed description of the test case is available in the [OpenFOAM documentation](https://www.openfoam.com/documentation/guides/latest/doc/verification-validation-naca0012-airfoil-2d.html). The flow conditions in the test case we consider are as follows:

- free stream velocity $U_{in} = 51.4815 m/s$
- kinematic viscosity $\nu = 8.58\times 10^{-6} m^2/s$
- chord length $c=1m$
- angle of attack $\alpha = 10^\circ$

The resulting Reynolds number is $Re=U_{in}c/\nu \approx 6\times 10^6$. Assuming incompressibility at the subsonic Mach number of $Ma=0.15$ is reasonable.

## OpenFOAM test case

Inspect the test case located at *test_cases/airfoil2D* and answer the following questions:

- which turbulence model is used?
- which transport equations are solved?
- are the boundary conditions set correctly?
- which quantities are extracted from the simulation?

To run the simulation, create a copy of the test case in your *run* folder and execute the *Allrun* script. The simulation completes in about 15min. Once the simulation is complete, try to address the following questions:

- how do the residuals for all transported quantities changes over time?
-how do the force coefficients compare to the reference data provided in the documentation?
- how do surface pressure and skin friction coefficients compare to the CFL3D reference data? (hint: the sampled surface data is located under *postProcessing/surface/*)
- how would you judge the quality of this simulation?
- how could the setup be improved?