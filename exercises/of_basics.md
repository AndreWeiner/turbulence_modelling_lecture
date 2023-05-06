![CC](https://i.creativecommons.org/l/by/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).

This document provides instructions for two exercise sessions. In the first part, you learn how to run and modify an existing simulation case. The second part focuses on runtime postprocessing.

# Basics of OpenFOAM simulations - part I

To get some hands-on experience with the OpenFOAM library, we rely on the official [tutorial guide](https://www.openfoam.com/documentation/tutorial-guide). In particular, we experiment with the lid-driven cavity tutorial, which is a common CFD test case. To complete this exercise, follow the instructions provided in [section 2.1](https://www.openfoam.com/documentation/tutorial-guide/2-incompressible-flow/2.1-lid-driven-cavity-flow#x6-60002.1) of the tutorial guide. In case you already have prior experience in using OpenFOAM, feel free to skip sections you are already familiar with.

# Basics of OpenFOAM simulations - part II

Simulations of turbulent flows often create enormous amounts of data due to the extensive range of spatial and temporal scales. In the analysis of such flows, we often compute statistical moments or spectral decompositions, both of which require temporally resolved snapshots of the flow fields. However, the higher the write frequency, the more data will be written to disk and needs to be processed in consecutive steps. To limit the amount of data written to disk while preserving a high temporal/spectral resolution, we can employ runtime postprocessing. Runtime postprocessing allows us, for example, to:

- compute statistical moments at runtime and write the converged statistics at the end of the simulation
- sample flow fields in a restricted subset of the simulation domain (point/probe, line, plane or surface) at a very high frequency

## Laminar flow past a cylinder

We simulate the laminar flow past a cylinder at $Re=100$ to explore the runtime postprocessing. The test case is located at *test_cases/cylinder2D* in the lecture repository. In case you have not yet created a local copy of the lecture repository, [download](https://github.com/AndreWeiner/turbulence_modelling_lecture/archive/refs/heads/main.zip) the zip archive or run `git clone`:
```
# navigate to wherever you plan to work and run
git clone git@github.com:AndreWeiner/turbulence_modelling_lecture.git
# navigate to the lecture repository's top level
cd turbulence_modelling_lecture
```
 In case you already copied the repository but cannot find the corresponding folder, refresh the repository's state by running `git pull` or download the repository again. To perform a simulation, it is advisable to create a copy of the base simulation in the run folder (`$FOAM_RUN`):
```
# assuming you are at the repository's top level
# (inside the turbulence_modelling_lecture folder)
cp -r test_cases/cylinder2D $FOAM_RUN
# navigate to the copy
cd $FOAM_RUN/cylinder2D
```
Run time processing in OpenFOAM is implemented by means of so-called *function objects*. [This page](https://www.openfoam.com/documentation/guides/v2112/doc/guide-function-objects.html) provides an overview of available function objects and settings defining the run time behavior. In the current state of the simulation, there is one function object to compute the force coefficients corresponding to the forces acting on the cylinder. You can inspect the function object by opening the *system/controlDict* file. Before running the simulation, we add a few more function objects.

## Probes

The [probes](https://www.openfoam.com/documentation/guides/latest/api/classFoam_1_1probes.html#details) function object allows to specify a number of points at which to sample one or more fields over time. The example below samples the pressure field at three different locations at every time step. Add the function object to your *controlDict* and add three more *probeLocations* located one diameter $d=0.1m$ further downstream the already specified probes (the main flow direction is along the positive $x$-direction; keep the other two coordinates of the three additional locations the same).
```
probes
    {
        type            probes;
        libs            (sampling);
        name            probes;
        writeControl    timeStep;
        writeInterval   1;
        startTime       0.0;
        fields          (p);
        fixedLocations  true;
        interpolationScheme cell;

        probeLocations
        (
            (0.3 0.15 0.005)
            (0.3 0.2 0.005)
            (0.3 0.25 0.005)
            // add more locations
        );
        includeOutOfBounds  false;
    }
```

## Line samples

A very common and important practice is the comparison between numerical and experimental results. Experiments often provide isolated time averaged data at important cross sections. To extract similar data from a simulation, we use line, plane or surface sampling. The example below shows how to sample along a line. Add this function object to the *controlDict* and complete the coordinates of start and end point such that the line starts at the cylinder's south pole and extends $20d$ in $x$-direction while being aligned with the $x$-axis. Tip: check the variables *radius*, *cylinderX*, and *cylinderY* in *system/blockMeshDict* to understand where the cylinder is located.

```
    line
    {
        type            sets;
        libs            (sampling);
        enabled         true;
        writeControl    timeStep;
        writeInterval   10;
        startTime       0.0;
        interpolationScheme cellPoint;
        setFormat       raw;
        fixedLocations  true;

        fields
        (
            U
        );

        sets
        {
            line
            {
                type    uniform;
                axis    distance;
                start   ( // add x and y // 0.005 );
                end     ( // add x and y // 0.005 );
                nPoints 100;
            }
        }
    }
```

## Mean flow

The last function object we add to the test case computes the mean fields of pressure and velocity as well as the corresponding time averaged fluctuations ([read more](https://www.openfoam.com/documentation/guides/v2112/doc/guide-fos-field-fieldAverage.html)). Note the modified *timeStart*; we do not want the initial transient stage to be taken into account in the averaging.

```
fieldAverage
{
    type            fieldAverage;
    libs            (fieldFunctionObjects);
    fields
    (
        U
        {
            mean        yes;
            prime2Mean  yes;
            base        time;
        }
        p
        {
            mean        yes;
            prime2Mean  yes;
            base        time;
       }
    );
    region          region0;
    enabled         true;
    log             true;
    timeStart       4;
    timeEnd         8;
    writeControl    writeTime;
    executeControl  timeStep;
    executeInterval 1;
 }
```

## Analysis and visualization

Now we are ready to run the simulation by executing the *Allrun* script. In case the simulation does not run as expected, check the *log.pimpleFoam* output, correct the *controlDict*, *Allclean* the case, and run the simulation again. This process has to be repeated until the solver runs as expected and computes $8s$ of flow evolution. On a standard laptop or workstation, the simulation should take roughly 20min.

### First inspection in ParaView

First, open the simulation in ParaView by creating an empty dummy file and then running ParaView:
```
touch post.foam
paraview post.foam
```
**Important:** before clicking *apply*, select *Decomposed Case* as case type in the left panel. Inspect the temporal evolution of velocity, pressure, and their mean fields.

### Analysis of forces, probes, and line samples

For a quick inspection of sampled quantities, [Gnuplot](http://www.gnuplot.info/) is a good choice. Any other tool you feel comfortable with is also fine. While the simulation is running, go to *postProcessing/forces/0* and visualize lift and drag:
```
# start gnuplot
gnuplot
# now we are in the gnuplot shell
# time, drag, lift are stored in columns 1, 2, 5
# plot drag over time; zoom in if necessary
gnuplot> plot 'coefficient.dat' u 1:2 w lines
# plot lift over time
gnuplot> plot 'coefficient.dat' u 1:5 w lines
# exit by pressing q followed by Enter
```
Now visualize the first pressure probe by navigating to *postProcessing/probes/0* and executing the commands below:
```
gnuplot
# visualize first probe over time; zoom in if necessary
gnuplot> plot 'p' u 1:2 w lines
```
Finally, we want to compute the time averages and fluctuations of the velocity components along the sample line. Since some more advanced data processing and array operations are needed, we resort to Python (or any other scripting language you feel comfortable with). The following code snippet demonstrates how to load and visualize a single line sample:
```
from glob import glob
import os
from os.path import join
import numpy as np
from pandas import read_csv
import matplotlib.pyplot as plt

plt.rcParams["figure.dpi"] = 160
sim_path = "./"

def read_line_data(path: str, names: tuple=("x", "Ux", "Uy"), usecols: tuple=(0, 1, 2)):
    data = read_csv(path, header=None, sep="\t", names=names, usecols=usecols)
    return data.values

line = read_line_data(join(sim_path, "postProcessing", "line", "2", "line_U.xy"))

plt.plot(line[:, 0]/0.1, line[:, 1:], label=(r"$u_x$", r"$u_y$"))
plt.xlabel(r"$x/d$")
plt.ylabel(r"$u_i/U_{in}$")
plt.legend()
plt.xlim(0, 20)
plt.title(r"velocity sample at $\tilde{t}=20$")
plt.show()
```
**Tip:** paste these lines of code in a Jupyter notebook to explore the data interactively.

The following steps are left in order to compute the time averages and fluctuations of the velocity components:

1. get a list of all time folders in *postProcessing/line/* (hint: use *glob*)
2. select only times $t>4s$
3. load the first sample line to infer the number of sample points
4. allocate two empty Numpy arrays of size $N_{points}\times N_{times}$ to store the temporal evolution of the two velocity components
5. loop over the time folders and fill up the arrays
6. compute and visualize the temporal mean and the Reynolds stresses






