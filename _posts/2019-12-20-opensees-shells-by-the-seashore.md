---
title: OpenSees Shells by the Seashore
category: Element Formulations
---

Other than state-of-the-art material and geometrically nonlinear frame
element formulations, it's fair to say OpenSees is not known for its breadth
of structural finite elements like quads, bricks, and shells. There are solid
elements for geotechnical applications and fluid elements for fluid-structure
interaction via the PFEM, but what's the story on shell elements in OpenSees?

You might be surprised to hear that `ShellMITC4`, a four node shell element
with membrane forces and drilling degrees of freedom based on Mixed
Interpolation of Tensorial Components (described here), was a relatively
early addition to OpenSees. In fact, it was on
[my Zip disk]({% post_url 2019-11-01-opensees-source-control-from-zip-to-git %}) of OpenSees source
code from 2001. Although `ShellMITC4` was not the only non-frame element at the
time, it was definitely the only shell element.  

The `ShellMITC4` element was written by Ed "C++" Love, a talented mechanician
who wanted to learn C++ by implementing finite elements in OpenSees. In
addition to the shell element, Ed wrote a couple bricks and a couple quads,
as well as the `block2D` and `block3D` meshing commands. You will find a
free vibration analysis using `ShellMITC4` and `block2D` in `Example7.1.tcl`
of [`EXAMPLES/ExampleScripts`](https://github.com/OpenSees/OpenSees/tree/master/EXAMPLES/ExampleScripts),
the directory of "OG" OpenSees examples. 

Ed's shell implementation was loosely coupled with the constitutive response,
i.e., he wrote elastic and fiber-based SectionForceDeformation objects for the
element's constitutive response instead of constructing the element with a
thickness and material properties. Unlike the frame elements that also use
_SectionForceDeformation_ objects, the section stress resultants have to be
in a specific order for `ShellMITC4`. Not a big deal.

Although Ed's shell element worked fine, he didn't write it with efficiency
in mind. Around 2011, Leopoldo Tesser, Diego Talledo, and Véronique Le Corvec
modified `ShellMITC4` to be more efficient. Formatting and large chunks of
source code are identical to the original implementation--only Ed Love would
name a helper function _LovelyEig_. However, Ed's name disappeared from the
`ShellMITC4` source code. [Pull request #187](https://github.com/OpenSees/OpenSees/pull/187)
shows it's never too late to right the history books.

---
<br>
Now there are several more shell elements in the [`SRC/element/shell`](https://github.com/OpenSees/OpenSees/tree/master/SRC/element/shell)
directory. Tesser et al wrote `ShellMITC9` as an extension of the four node
formulation. This is the only nine node shell element in OpenSees.

Xinzheng Lu and co-contributors implemented `ShellDKGT` and `ShellDKGQ`,
three node and four node, respectively, discrete Kirchhoff thin shells with
drilling degrees of freedom. Lu et al also implemented geometrically
nonlinear versions `ShellNLDKGT` and `ShellNLDKGQ`.

Jose Abell wrote a three node membrane and drilling shell element,
`ShellANDeS`, based on formulations described in a series of papers,
starting with
[Alvin et al (1992)](https://doi.org/10.1016/0168-874X(92)90033-9), by
Carlos Felippa and collaborators.
This is the only shell element in
OpenSees that doesn't use _SectionForceDeformation_ objects. Instead the
element takes the shell thickness and elastic properties in the constructor.

---
<br>
In the [`SRC/material/section`](https://github.com/OpenSees/OpenSees/tree/master/SRC/material/section) directory are three section models for shell
elements: _ElasticMembranePlateSection_, _MembranePlateFiberSection_ (MPFS),
and _LayeredShellFiberSection_ (LSFS). The former takes elastic properties and
a shell thickness while the latter two discretize the section thickness into
fibers or layers, each of which uses an _NDMaterial_ object for its
stress-strain response. To integrate stresses through the section thickness,
MPFS uses five-point Gauss-Lobatto integration while LSFS uses midpoint
integration over a user-specified number of layers--otherwise, MPFS and LSFS
are the same.

Over in the [`SRC/material/nD`](https://github.com/OpenSees/OpenSees/tree/master/SRC/material/nD) directory you'll find several "PlateFiber"
material models divided in two categories--direct formulations and wrapper
classes. The direct formulations include _ElasticIsotropicPlateFiber_ and
_J2PlateFiber_, which has nonlinear strain hardening. There is also a
_J2PlateFibre_ (British spelling) that uses a linear-strain hardening rule.

There are three flavors of wrapper classes. The first, _PlateFiberMaterial_,
uses static condensation of a three-dimensional _NDMaterial_ object to make
$$\sigma_{33}$$=0, the shell fiber stress condition. The second,
_PlateFromPlaneStressMaterial_, adds elastic shear stresses to a plane
stress _NDMaterial_ object to give the shell fiber stress condition and in
doing so ignores potential interaction of in-plane stresses with out-of-plane
shear stresses. The third wrapper class, _PlateRebarMaterial_, uses a
_UniaxialMaterial_ object and an orientation angle to represent reinforcement
in shell elements.  In theory, these wrapper classes should work with any
material model.

All of the shell elements and constitutive models described in this post have
`OPS_XYZ` functions, so they should be usable in both the Tcl executable and
the Python module.

---
<br>
_This post was suggested by a reader of the blog. If you would like to
suggest a topic, please let me know_.