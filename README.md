# CFD Analysis of a Pitching NACA 0012 Airfoil — Dynamic Stall

This repository contains the OpenFOAM case developed for the numerical analysis of a NACA 0012 airfoil undergoing harmonic pitching motion under dynamic-stall conditions.

The case was developed as part of a Bachelor's thesis in Aerospace Engineering and was used to reproduce and validate one of the experimental configurations reported by McAlister, Carr and McCroskey in *Dynamic Stall Experiments on the NACA 0012 Airfoil*, NASA Technical Paper 1100.

The numerical model is based on a two-dimensional incompressible RANS simulation performed with OpenFOAM 11 using the transient solver `pimpleFoam` and the Spalart–Allmaras turbulence model.

The reference case corresponds to a NACA 0012 airfoil with chord `c = 1 m`, Reynolds number `Re = 2.5 × 10^6`, freestream velocity `U∞ = 25 m/s`, mean angle of attack `15°`, pitching amplitude `±10°` and reduced frequency `k = 0.10`. The corresponding angular frequency is `ω = 5 rad/s`, the oscillation period is `T = 1.2566 s` and the rotation centre is located at the quarter chord, `x/c = 0.25`. The time step is fixed to `2 × 10^-5 s` and three complete oscillation cycles are simulated.

The effective angle of attack follows the law `alpha(t) = 15° + 10° sin(5t)` and therefore varies between approximately 5° and 25°.

The problem is treated as two-dimensional by extruding the mesh in the spanwise direction using a single cell and applying the `empty` boundary condition to the `frontAndBack` patches. The computational domain extends approximately 32 chord lengths in the streamwise direction and 24 chord lengths in the vertical direction. The structured mesh contains approximately 11,114 cells and is refined near the airfoil surface, leading edge and wake region. The mesh is already included in `constant/polyMesh`, so no separate mesh-generation step is required.

The pitching motion is prescribed in `0/pointDisplacement` using the `angularOscillatingDisplacement` boundary condition. The main parameters are `origin (0.25 0 0)`, `axis (0 0 -1)`, `angle0 0`, `amplitude 0.1745329252` and `omega 5`. The amplitude corresponds to 10° and the rotation centre is located at the quarter chord.

The mesh motion is controlled by `constant/dynamicMeshDict` using the `displacementLaplacian` motion solver. The mesh diffusivity is based on the inverse distance from the airfoil surface, so that the largest deformation is concentrated near the moving profile and progressively decreases toward the external boundaries.

The repository follows the standard OpenFOAM case structure. The `0/` directory contains the initial fields, boundary conditions and prescribed airfoil motion; the `constant/` directory contains the computational mesh, fluid properties, turbulence-model settings and dynamic-mesh configuration; the `system/` directory contains the numerical schemes, solver settings, simulation controls and post-processing function objects.

Before running the simulation, the mesh should be checked with `checkMesh`. The reference mesh should return `Mesh OK` and contains approximately 11,114 cells, with a maximum non-orthogonality of 40.14°, a mean non-orthogonality of 8.85°, a maximum skewness of 0.784 and a maximum aspect ratio of 965.6.

Once the mesh has been checked, the simulation can be started with `pimpleFoam`. For long runs, the solver output can be saved using `pimpleFoam > log.pimpleFoam 2>&1`, while the simulation progress can be monitored with `tail -f log.pimpleFoam`.

The reference case uses a constant time step of `2 × 10^-5 s` with `adjustTimeStep no`. Three complete oscillation cycles are simulated, corresponding to a final time of approximately 3.77 s.

The aerodynamic coefficients `CL`, `CD` and `CM` are evaluated using the `forceCoeffs` function object defined in `system/controlDict`, while the wall parameter `y+` is evaluated separately using the `yPlus` function object. The corresponding results are written automatically inside the `postProcessing` directory.

The flow field can be visualized in ParaView using `paraFoam`. The main quantities of interest are the velocity field, pressure field, wake development, flow separation and aerodynamic response. For the dynamic-stall analysis, it is particularly useful to compare pitch-up and pitch-down conditions at the same instantaneous angle of attack in order to highlight the hysteretic behaviour of the unsteady aerodynamic response.

The numerical results were compared with the experimental measurements reported by McAlister, Carr and McCroskey in NASA Technical Paper 1100. The validation focuses mainly on the normal-force coefficient `CN`, obtained during post-processing, and the pitching-moment coefficient `CM` over a complete oscillation cycle. The CFD model reproduces the main qualitative features of the dynamic-stall response and the associated hysteresis loops, while quantitative differences remain in the prediction of the stall angle and peak aerodynamic loads.

Reference: McAlister, K. W., Carr, L. W. and McCroskey, W. J., *Dynamic Stall Experiments on the NACA 0012 Airfoil*, NASA Technical Paper 1100, 1978.

Author: Anna Galli — Bachelor's thesis project in Aerospace Engineering.
