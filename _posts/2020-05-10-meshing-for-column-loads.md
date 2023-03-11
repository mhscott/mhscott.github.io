---
title: Meshing for Column Loads
category: Nonlinear Analysis
---

For material nonlinear analysis of frame models, you can
[improve the computed response]({% post_url 2020-02-23-a-tale-of-two-element-formulations %}) by using more displacement-based elements or more integration points in a force-based element. The material nonlinearity occurs inside the basic system, also known as the natural system or the kernel.

To capture geometric nonlinearity due to large displacements, you have to go outside the basic system with the P-\Delta or corotational transformation between the basic and global systems.

But capturing geometric nonlinearity inside the basic system, i.e., P-\delta effects, is more complicated. You need to use a different element formulation, e.g., nonlinear strain in the displacement-based formulation or curvature-based displacement interpolation (CBDI) in the force-based formulation.

Fortunately, the large displacement transformation outside the basic system is independent of what's going on inside the basic system. So, instead of using a more complex element formulation, you can use a small strain formulation for each element in a corotational mesh and get P-\delta effects “for free”.

Let's demonstrate the idea with a 15 ft long W10x54 column buckling about its weak axis. The section area is _A_=15.8 in<sup>2</sup> and the second moment of section area about the weak axis is _I_=103 in<sup>4</sup>. The material properties are _E_=29,000 ksi and _F<sub>y</sub>_=50 ksi.

![W10x54 column](/assets/images/ColumnW10x54.png)

To trigger buckling in the subsequent analyses, the nodal locations are defined by a first mode buckled shape with an initial imperfection of L/1000 at mid-height of the column.

## Elastic Buckling

The elastic buckling load for the column, _P<sub>e</sub>_=910 kip, is obtained from the Euler equation _P<sub>e</sub>_=$$\pi^2EI/L^2$$. Using a corotational mesh of Bernoulli beam elements defined by _E_, _A_, and _I_, we should converge to this value.

The axial load-displacement response is shown below for _N<sub>ele</sub>_=2, 4, and 6 elements. The response is computed using displacement control on the vertical DOF at the top of the column. Consistent with finite element theory, the coarser mesh gives higher apparent strength. Four elements does a good job and six elements is a little better.

![Axial load-displacement response](/assets/images/ColumnElastic.png)

We will use six elements for the analyses in the following section.

## Inelastic Buckling

This column is not slender enough to buckle elastically because the crushing load, _P<sub>c</sub>=F<sub>y</sub>A_=790 kip, is less than the Euler buckling load. Furthermore, Table 4-1a of the Steel Manual reports the nominal strength for this column to be _P<sub>n</sub>_=550 kip. In other words, the column will buckle inelastically. (I suppose we could have started the examples section of the post here, but it's always fun to check the Euler buckling load.)

To capture inelastic buckling, we can use material nonlinear beam-column elements in the corotational mesh. Since we are using a discretized mesh to capture the geometric nonlinearity, we might as well go with the displacement-based formulation for the material nonlinearity.

A fiber discretization about the weak axis simulates the force-deformation response at each integration point. The stress-strain response of each fiber is bilinear with the given _F<sub>y</sub>_ and _E_, and strain-hardening ratio $$\alpha$$=0.005. There are two fibers in the web and eight in each flange.

![Fiber discretization about weak axis](/assets/images/WFSectionWeak.png)

The computed axial load-displacement relationship is shown below, again using displacement control. As expected, we come in under the crushing load; however, we over-predict the nominal strength by about 50 kip.

![Axial load-displacement response](/assets/images/ColumnPlastic.png)

Why are we over-predicting the nominal compressive strength reported in the Steel Manual? Recall that the Steel Manual uses an empirical relationship to account for residual stresses in the compressive strength tables. So, we should get closer to the nominal strength if we use residual stresses in the analysis.

To this end, we can use the `InitStressMaterial` in OpenSees to define a residual stress pattern across the fiber section. The linear approximation of the Lehigh pattern is a simple approach.

![Fiber discretization and residual stress pattern](/assets/images/WFSectionResidual.png)

Recomputing the axial load-displacement response with _F<sub>r</sub>=0.2F<sub>y</sub>_ (compression) and $$F_t=F_r(b_ft_f)/(b_ft_f+d_wt_w)$$ (tension) gives the results shown below. Much better!

![Axial load-displacement response](/assets/images/ColumnPlasticResidual.png)

Note that it's a good idea to get the initial stresses into equilibrium with one analysis step prior to applying loads with displacement control. It took me a while to remember this after trying to wrap my head around why the displacement-controlled analysis failed well before reaching the nominal compressive strength.
