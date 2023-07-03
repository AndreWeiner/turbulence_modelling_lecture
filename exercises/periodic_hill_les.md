![CC](https://i.creativecommons.org/l/by/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).

# Flow over periodic hills - LES

In this exercise, we revisit the flow over periodic hills, which we attempted to compute with a direct numerical approach in one of the previous exercises. The present exercise consists of two parts. In part I, we employ a wall-resolving LES, whereas in part II, we introduce wall functions to set up a wall-modeled LES.

## Part I: wall-resolving LES

We use the same setup as in [this exercise](periodic_hills_dns.md) as a starting point for the LES. The following steps guide the process:

- make a copy of the test case *test_cases/periodic_hills_base* in the run folder and navigate to the copy
- set the *simulationType* to LES and add the dictionary below to specify the subgrid-scale model; a suitable value for the constant *Cw* can be found in the article by [Fröhlich et al. (2005)](https://doi.org/10.1017/S0022112004002812), subsection *Subgrid-scale modeling*; a copy of the article is available on StudIP

```
LES
{
    LESModel        WALE;
    turbulence      on;
    printCoeffs     on;
    delta           cubeRootVol;
    WALECoeffs
    {
        Cw          ???;
    }
}
```

- create a new file in *0.orig* named *nut* for the eddy viscosity, e.g., by creating a copy of *p*; adjust the object name and the dimensions; set a uniform fixed value of zero as boundary condition for the walls
- in the *controlDict*, add function objects to compute $y^+$ and $k$; moreover, add the resolutionIndex function object to assess how well the mesh resolves the turbulent spectrum (see below and [here](https://develop.openfoam.com/Development/openfoam/-/merge_requests/569)); for improved visualization, it is helpful to also compute the temporal mean of the *PopeIndexField*; you may also try an alternative resolution index like the *CelikNuIndex*

```
resolutionIndex1
{
    // Mandatory entries
    type            resolutionIndex;
    libs            (fieldFunctionObjects);
    model           PopeIndex;
    result          PopeIndexField;
    // Optional entries
    U               U;
    UMean           UMean;
    k               "turbulenceProperties:k";
    delta           delta;
    includeKnum     true;
    Cn              1.0;
    log             true;
    timeStart       1.0;
    executeControl  timeStep;
    executeInterval 1;
    writeControl    writeTime;
    writeInterval   -1;
}
```

Now we a ready to run the simulation. Once the simulation is complete, evaluate the following quantities:

- compare the mean velocity profiles to that of the DNS and the reference data
- evaluate the resolution index in ParaView
- evaluate the near-wall resolution in terms of $y^+$ and $x^+/z^+$; for the streamwise and depth components, you can assume that $\Delta x^+ = \Delta z^+ = \sqrt{A}_f y^+/y_1$ ($A_f$ - face area of wall face, $y_1$ - distance of first cell center to the wall); the distance of the first cell center may be estimated by selecting and extracting a cell adjacent to the wall; the face area at the wall can be computed by selecting only the wall patch(es) and employing the *CellSize* filter; finally, the *Calculator* enables converting $y^+$ to $x^+$

## Part II: wall-modeled LES

In the second part of this exercise, we will try to speed up the simulation created in the first part while keeping the accuracy roughly unchanged. The key ingredient to achieve this goal is a so-called wall functions. Wall functions allow placing the first cell center at a distance of $30 < y^+_1 < 200$ away from the wall. The particular wall function we employ is based on Spalding's function and could be applied also in the buffer or viscous layer without issues. Thanks to this reduced resolution requirement, we can perform the same simulation with significantly fewer cells.

To get started, create a copy of the setup created in part I and run the *Allclean* script. To reduce the mesh resolution normal to the channel walls, the *system/blockMeshDict* needs to be modified. In the base mesh, the first cell layer had width such that $y^+_1 < 3$ for the top and bottom walls. Accordingly, when employing wall functions, the first cell width can be at least ten times larger. To reduce the resolution in $y$,

1. reduce the number of cells in $y$
2. reduce the grading towards to the top and bottom walls

[This document](http://www.wolfdynamics.com/wiki/meshing_OF_blockmesh.pdf) provides a good introduction to *blockMesh*. Pages 33-36 explain the so-called multi-grading, which is employed in this test case. Remember that you can use ParaView to measure the width of the first cell graphically.

In OpenFOAM, the wall function corrects the shear stress at the wall by modifying the eddy viscosity on the wall patch. Therefore, the wall function is applied as boundary condition to the *nut* field. Refer to the [documentation](https://doc.openfoam.com/2306/tools/processing/boundary-conditions/rtm/derived/wall/nutUSpaldingWallFunction/) of the *nutUSpaldingWallFunction* to find an example setup.

After running the modified setup, compare the following quantities to the wall-resolved LES:

- velocity profiles and Reynolds stresses
- $y^+$ values at the top and bottom walls
- execution time