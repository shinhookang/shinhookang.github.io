---
layout: single
title: "Research"
permalink: /research/
author_profile: true
---
  
My research primarily focuses on numerical methods for partial differential equations.
This includes high-order spatial and temporal discretization methods
such as (hybridized) discontinuous Galerkin (DG), 
finite volume (FV), exponential integrators and implicit-explicit (IMEX) methods. 
Developing scalable numerical algorithms for modern computer architectures is also of interest.
Recent work is to 
develop stable time stepping schemes for coupling multiphysics systems, e.g., atmosphere and ocean.


# Research Highlights

- [Mass-Conserving IMEX Coupling](#mass-conserving-IMEX-coupling)
- [Scalable Time Integrators](#exponential-dg)
- [High-order Spatial Discretizations](#hybridized-dg-methods-for-a-linear-degenerate-elliptic-equations)
- [Geophysical Flows](#a-coupled-implicit-hdg-and-explicit-dg-methods-for-shallow-water-systems)
- [Remote Sensing](#temperature-and-moisture-retrievals-from-hyperspectral-measurements)
  
## Mass Conserving IMEX Coupling

Earth system models are composed of coupled components that separately model systems such as the global atmosphere, ocean, and land surface. While these components are well developed, coupling them in a single system can be a significant challenge. Computational efficiency, accuracy, and stability are principal concerns. In this study we focus on these issues. In particular, implicit-explicit (IMEX) tight and loose coupling strategies are explored for handling different time scales. For a simplified model for the air-sea interaction problem, we consider coupled compressible Navier–Stokes equations with an interface condition. Under the rigid-lid assumption, horizontal momentum and heat flux are exchanged through the interface.

IMEX coupling methods solve one domain explicitly and the other implicitly. To enhance computation efficiency, we adapt IMEX time integrators, which can handle scale-separable stiffness or geometrically induced stiffness, as an implicit solver. Furthermore, we employ a horizontally explicit and vertically implicit (HEVI) approach where solutions are obtained column by column; hence, the resulting linear system is significantly reduced compared with two-dimensional IMEX methods.

We conduct the simulation with the IMEX method. The evolution of temperature fields is shown for $t \in [0,500]$. 
Kelvin-Helmholtz waves are well developed at $t=100$ and start to diffuse while mixing fluids. 
Meanwhile, because of the heat and horizontal momentum exchange, fluid on the bottom domain cools near the interface and moves along the outside of vortex as time passes.

<p float="left">
<img src="../files/khi_imex_coupling.jpg" width="850" />
</p>


Total mass is conserved with IMEX coupling methods regardless of tight or loose coupling methods. The total mass losses for IMEX tight and loose coupling methods are within $\mathcal{O}(10^{−13})$. This result makes sense because we do not exchange the mass across the interface but adjust the wall temperature in the interface. This rigid-lid condition blocks the vertical motion of the interface: no mass flux is allowed, and hence the total mass is conserved. As for the total energy loss, a peak is observed near $t=100$ (when strong KHI is observed above), and then the total energy loss decreases as time passes.

<p float="left">
<img src="../files/massloss_imex_coupling.jpg" width="850" />
</p>

- [Mass-Conserving Implicit-Explicit Methods for Coupled Compressible Navier-Stokes Equations](https://arxiv.org/abs/2101.09263)

## Exponential DG
 
We propose an Exponential DG approach for numerically solving partial differential equations (PDEs). The idea is to decompose the governing PDE operators into linear (fast dynamics extracted by linearization) and nonlinear (the remaining after removing the former) parts, on which we apply the discontinuous Galerkin (DG) spatial discretization. The resulting semi-discrete system is then integrated using exponential time-integrators: exact for the former and approximate for the latter. 

By construction, our approach is stable with a large Courant number (Cr > 1); supports high-order solutions both in time and space; is computationally favorable compared to IMEX DG methods with no preconditioner; and requires comparable computational time compared to explicit RKDG methods.

<p float="left">
<img src="../files/expo_accuracy.png" width="850" />
</p>
   
Exponential DG approach is scalable in a modern massively parallel computing architecture by exploiting Krylov-subspace matrix-free exponential time integrators and compact communication stencil of DG methods. 
The following plot shows good strong scalability up to 41664 cores (the maximum number of cores in Skylake system in [TACC](https://www.tacc.utexas.edu)). An isentropic vortex is simulated for three-dimensional Euler equations. 
 
<p float="left">
<img src="../files/expo_strongscalability.png" width="850" />
</p>

- [A scalable exponential-DG approach for nonlinear conservation laws: with application to Burger and Euler equations](https://arxiv.org/abs/2011.01316)


## Hybridized DG methods for a Linear Degenerate Elliptic Equations

We develop a high-order hybridized discontinuous Galerkin (HDG) method for a linear degenerate elliptic equation arising from a two-phase mixture of mantle convection or glacier dynamics. 
Both phenomena can be described by a two-phase mixture model, in which the mixture of the fluid and the solid is described by the porosity $\phi$(i.e., $\phi>0$ implies the fluid-solid two-phase and $\phi=0$ means the solid single-phase region). The challenge is when the porosity vanishes because the system degenerates, which make the problem difficult to solve numerically.
We start by scaling variables to obtain the well-posedness. 
Then we spatially discretize the system using the upwind HDG framework. 

Below shows the contours of the physical pressure $\tilde{p}$ and 
the scaled pressure $p$. We observe that the pressure $\tilde{p}$ changes 
smoothly in the two-phase regions, but abruptly becomes zero 
in the one-phase region. The sudden pressure jump on the intersection 
is alleviated with the use of the scaled pressure $p$.

<p float="left">
<img src="../files/de_smooth.png" width="850" />
</p>

We have modified the upwind HDG flux to accommodate the degenerate (one-phase) region. When the porosity vanishes, the unmodified HDG system becomes ill-posed because the stabilization parameter associated with the HDG flux disappears. To handle this, we introduce the generalized stabilization parameter that is composed of the upwind based parameter in the two-phase region and a positive parameter in the one-phase region. This enabled us to develop a high-order HDG method for a linear degenerate elliptic equation.
For the degenerate case with a smooth solution, the convergence rate of 
$k + \frac{1}{2}$ is observed for the scaled pressure $p$. We can further enhance the HDG solutions in smooth-regions by post-processing. The post-processed pressure $p^\star$ converges to the true solution 
with the rate of $k + \frac{3}{2}$. 

<p float="left">
<img src="../files/de_smooth_conv.png" width="850" />
</p>

- [A hybridized discontinuous Galerkin method for a linear degenerate elliptic equation arising from two-phase mixtures](https://doi.org/10.1016/j.cma.2019.03.018)

## A Coupled Implicit HDG and Explicit DG methods for Shallow Water Systems

We propose IMEX HDG-DG schemes for planar and spherical shallow water systems.
Of interest is subcritical flow, 
where the speed of the gravity wave is faster than that of nonlinear advection.
In order to simulate these flows efficiently,
 we split the governing system into a stiff part describing the gravity wave
  and a non-stiff part associated with nonlinear advection. 


IMEX HDG-DG approaches are more economical than our previous work on IMEX DG
due to the fewer number of coupled degrees of freedom 
in the context of a direct solver. 
Compared to standard fully implicit methods, 
they are advantageous since only one linear solve is needed for each stage per time-step.
With a low Froude number, 
the IMEX HDG-DG methods can be more economical than the explicit RK 
when the desired accuracy is relaxed.
Below is the summary of temporal convergence study for moving vortex with $Fr=0.01$. 
When the saturation error level is $\mathcal{O}(10^{-6})$, 
ARS2 and ARS3 are six and four times faster than RK2 DG. 

<p float="left">
<img src="../files/imex_tconv.png" width="850" />
</p>

A zonal jet,
a wind field along a latitude line and geostrophically balanced height field, 
is initialized in the northern hemisphere. 
Then, the height field is perturbed by adding a smoothly localized bump 
to the center of the jet, which causes barotropic waves to evolve in time. 
The vorticity field computed from ARS2 HDG-DG is comparable to the [work](https://rmets.onlinelibrary.wiley.com/doi/pdf/10.1002/qj.2474).

<p float="left">
<img src="../files/imex_sswe.png" width="850" />
</p>

- [IMEX HDG-DG: A coupled implicit hybridized discontinuous Galerkin and explicit discontinuous Galerkin approach for shallow water systems](https://doi.org/10.1016/j.jcp.2019.109010)

## Temperature and Moisture Retrievals from Hyperspectral Measurements

An Atmospheric Emitted Radiance Interferometer [(AERI)](https://www.ssec.wisc.edu/aeri/), which measures downwelling radiances, has been in operation at Anmyeon-do, South Korea, since June 2010. 
Temperature and moisture profiles with high temporal and vertical resolution can be retrieved from the measured AERI spectrum through the retrieval algorithm [AERIPROF](https://www.arm.gov/capabilities/vaps/aeriprof).

Below shows the spectra measured by the AERI on 26 May 2010 (case 1) and on 23 March 2011 (case 2). The air temperature at ground level in case 1 ($290.8 K$) was larger than in case 2 ($276.7 K$). Precipitable water vapor (PWV) in case 1 ($2.02 cm$) was higher than in case 2 ($0.38 cm$). The $CO_2$ bands are sensitive to a temperature sounding. The radiances in the $CO_2$ bands are higher in case 1 than in case 2. The transparency of the $H_2O$ bands and the atmospheric window is sensitive to the column water amount. The transparency of the $H_2O$ bands and the atmospheric window bands in case 1 is reduced compared to case 2 counterpart. The two measured spectra show good agreement with the observed temperature and PWV. 

<p float="left">
<img src="../files/aeri_radiance.png" width="850" />
</p>

In this work, we improve retrieval performance by adjusting a bias spectrum and regression coefficients for Anmyeon-do. 
The bias spectrum was recomputed from 
the coincident radiosondes during the field experiments and 
regression coefficients were obtained from local radiosondes and associated simulated spectral radiances through principal component analysis (PCA). 
The reduced (a) RMS errors and (b) bias profiles between the radiosondes are observed. The modified statistical regressions show better performance than the original statistical regression in the lower troposphere ($1000–700 hPa$).

<p float="left">
<img src="../files/aeri_temperature.png" width="850" />
</p>

- [Improvement of AERI T/q retrievals and their validation at Anmyeon-do, South Korea](https://doi.org/10.1175/jtech-d-12-00029.1)
