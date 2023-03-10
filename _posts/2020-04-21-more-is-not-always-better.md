---
title: More Is Not Always Better
category: Element Formulations
---

I sometimes run across simulations where frame member response is computed using displacement-based beam-column elements with 
more than two Gauss points per element. Displacement-based frame elements require at least two Gauss points to 
capture the exact solution for a linear-elastic, prismatic member.

While it is well known that you can improve simulated frame response by increasing the number of integration points in a 
_force-based_ beam-column element, does the same hold true when using more than two integration points in a displacement-based element? 
Let's find out using a model of a W14x90 steel member that appeared in a 
[previous post]({% post_url 2020-02-23-a-tale-of-two-element-formulations %}) comparing the displacement-based and force-based formulations.

![Displacement-based element with Gauss integration](/assets/images/dbbcNProppedCantilever.png)

The member resists a constant axial load, _N=0.2F<sub>y</sub>A_, and a moment, _M_, that
increases from zero to two times the yield moment, _M<sub>y</sub>=F<sub>y</sub>S_. The section
area, _A_, and strong axis section modulus, _S_, for a W14x90 are found in the Steel Manual.

Interaction of the axial force and bending moment along the member is captured by
fiber-discretized cross-sections. The stress-strain response of each fiber is bilinear with
_F<sub>y</sub>_=50 ksi, _E_=29,000 ksi, and $$\alpha$$=0.005.

We will examine the global and local response obtained from one displacement-based beam-column element (`dispBeamColumn` in OpenSees) with _N<sub>p</sub>_=3, 4, and 5 Gauss-Legendre integration
points. The analysis does not include geometric nonlinearity within the element.

The moment-rotation and moment-axial displacement response are shown below. Increasing the
number of integration points does not improve the computed solution. Although there are some
differences in the post-yield response, neither for better nor worse, the simulation
consistently over-estimates the yield point.

![Load-displacement response for displacement-based element](/assets/images/dbbcNBeam.png)

The local flexural response at the final load step is shown below. As expected for the displacement-based formulation, the curvature distribution remains linear across the element and there is no significant change in the distribution as the number of integration points increases. The bending moments computed at each integration point do not converge to the exact solution???they merely scurry around to satisfy weak equilibrium.

![Moment-curvature distribution for displacement-based element](/assets/images/dbbcNBeamProfileM.png)

Similar trends are observed for the local axial response. The axial deformation is constant along the element, as expected, but shifts a bit as the number of integration points increases. Thanks to weak equilibrium, the axial force at each integration point is pretty far from the expected solution; however, the weighted sum over all points is equal to the expected result of 265 kip compression in all cases.

![Axial force-deformation distributions for displacement-based element](/assets/images/dbbcNBeamProfileN.png)

So, my assessment is that increasing the number of integration points in a displacement-based element isn't going to help you. It won???t really hurt you either; however, the amount of computational time spent on section state determinations can start to add up, especially for fiber sections, and cost you valuable wall time for no added benefit.

Two integration points per displacement-based element is all you need???you???re going to have to refine the mesh anyway.
