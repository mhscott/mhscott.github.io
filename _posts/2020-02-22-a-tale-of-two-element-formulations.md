---
title: A Tale of Two Element Formulations
category: Element Formulations
---

The question of whether to use the force-based or displacement-based
formulation for material nonlinear frame analysis is one that comes up a lot.
The answer depends on a few factors, mostly the material and the element length.

To get a sense of the basic issues, I will compare the two element
formulations with a numerical example from my graduate course in Eastchester
on nonlinear structural analysis. I'm not going to write any equations, but
you can find more information on the formulations in
[Neuenhofer and Filippou (1997)](https://doi.org/10.1061/(ASCE)0733-9445(1997)123:7(958)).

The model is of a W14x90 steel member that supports a constant axial load,
_N_, and an increasing moment, _M_, applied about the member strong axis.
The axial load is _N=0.2F<sub>y</sub>A_ and the applied moment increases from zero to
_2M<sub>y</sub>_, where _M<sub>y</sub>=F<sub>y</sub>S_. The section area, _A_, and section modulus,
_S_, for a W14x90 are found in the Steel Manual.

![Mesh of displacement-based frame elements](/assets/images/mrdbProppedCantilever.png)

A fiber discretization of the member cross-section captures axial-moment
interaction. The stress-strain response of each fiber is bilinear with
_F<sub>y</sub>_=50 ksi, _E_=29,000 ksi, and $$\alpha$$=0.005. We are loading this
thing into ridiculousness, allowing indefinite strain-hardening and ignoring
geometric nonlinearity.

## Displacement-Based Formulation

The displacement-based formulation (`dispBeamColumn` in OpenSees) uses shape
functions for the axial and transverse displacement fields along each element.
The distributions of section deformation interpolated along each element are
constant axial deformation and linear curvature. The section forces satisfy
weak equilibrium with the element end forces.

Consistent with finite element theory, we improve the computed solution by
increasing the number of elements along the member length (h-refinement). We
will look at the global and local response of the W14x90 using
_N<sub>ele</sub>_=1, 4, and 8 elements. Each element has two Gauss integration
points.

The moment-rotation and moment-axial displacement response of the beam are
shown below. The one element solution severely overestimates the yield point;
however, this improves as the number of elements increases. The
moment-rotation response using eight elements is right on the exact solution,
but the moment-axial displacement response remains slightly off.

![Load-displacement response for displacement-based elements](/assets/images/mrdbBeam.png)

The local flexural response at the final load step is shown below. The curvature distribution along the member is piecewise linear and discontinuous between elements. As the number of elements increases, the curvature approximation improves. Furthermore, the bending moments computed at each Gauss point start to line up with the exact solution as the number of elements increases.

![Moment-curvature distributions for displacement-based elements](/assets/images/mrdbBeamProfileM.png)

The local axial response is a little more revealing. The distribution of axial deformation along the member is piecewise constant. It will take more elements than is practical to get a decent approximation of the exact solution. The axial forces at each Gauss point appear to be all over the place, but not really. For each element, they average out to the exact solution.

![Axial force-deformation distributions for displacement-based elements](/assets/images/mrdbBeamProfileN.png)

The averaging of internal axial forces is a manifestation of weak equilibrium. The same thing happens with the flexural response, but there's a linear weighting function that makes the weak equilibrium less obvious.

At some point, saying that displacement-based elements are bad because of the axial response becomes a
[straw man argument](https://en.wikipedia.org/wiki/Straw_man).
If we used a higher order axial displacement function with an internal node (p-refinement), the axial response would be much better.

## Force-Based Formulation

The force-based formulation (`forceBeamColumn` in OpenSees) uses the known equilibrium solution for a beam--the one you learned in statics--to interpolate constant axial force and linear bending moment along the element length. Compatibility between section deformations and element deformations is satisfied in an average sense.

To improve the computed solution with force-based elements, we increase the number of integration points in an element. For the W14x90, we will use one element with _N<sub>p</sub>_=3, 4, and 5 integration points. The integration is Gauss-Lobatto, which places an integration point at the element ends to capture the extreme flexural response.

![Force-based frame element with Gauss-Lobatto integration](/assets/images/fbbcProppedCantilever.png)

The global moment-rotation and moment-axial displacement response of the beam are shown below. In all cases, the yield point is captured exactly. The post-yield moment-rotation response is too flexible with three Lobatto points, but this improves as we use more integration points. The moment-axial displacement response is a little bizarre though.

![Load-displacement response for force-based element](/assets/images/fbbcBeam.png)

The local flexural response is shown below for the final load step. The internal bending moment adheres to a linear distribution along the element and approaches the exact solution as the number of integration points increases. Likewise, the curvature at each integration point improves as the number of integration points increases.

Clearly, just because it's a force-based element does not mean the exact equilibrium solution is found. This is a common misconception. All that you get is a linear distribution of internal bending moment, not the exact distribution.

![Moment-curvature distributions for force-based element](/assets/images/fbbcBeamProfileM.png)

The local axial response also improves as the number of integration points increases. In all cases, the internal axial force matches the applied axial load, which is good. Go statics!

![Axial force-deformation distributions for force-based element](/assets/images/fbbcBeamProfileN.png)

If axial-moment interaction is a concern, e.g., reinforced concrete moment frames, go with force-based elements. If you are constrained to use several short elements, e.g., for a pile with p-y soil springs every few feet, then I'd recommend displacement-based elements.

Other formulations are available; however, the displacement-based and force-based formulations give bounds on what to expect from alternative formulations.

There are other issues, such as localization, to address with frame finite elements. But this post is getting lengthy and should be wrapped up. Let me know if you have any questions in the comments section below.

---
<br>
In the spirit of full disclosure, the exact solutions shown throughout this
post were obtained using a fine mesh of force-based elements.

_This post was suggested by a reader of the blog. If you would like to
suggest a topic, please let me know._
