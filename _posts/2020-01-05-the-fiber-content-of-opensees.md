---
title: The Fiber Content of OpenSees
category: Constitutive Models
---

Although fiber sections are not unique to OpenSees, where else can you choose
from 23 uniaxial concrete models and 18 uniaxial steel models to define a
reinforced concrete section? There's also a handful of multiaxial material
models that capture the interaction of axial and shear stresses in beam
fibers, as well as fiber models for [shells]({% post_url 2019-12-20-opensees-shells-by-the-seashore %}).

Remo De Souza developed the infrastructure for fiber sections as part of his
class project in CE 224 Computer-Aided Civil Engineering at UC Berkeley. For
his Ph.D. dissertation, Remo extended the geometrically nonlinear CBDI
force-based formulation to include material nonlinearity and developed a
three-dimensional corotational formulation. He also implemented
`nonlinearBeamColumn`, the initial version of the force-based frame element.
Remo is an unsung hero of OpenSees.

In his implementation of fiber sections, Remo created the [_FiberSectionRepres_](https://github.com/OpenSees/OpenSees/blob/master/SRC/material/section/repres/section/FiberSectionRepr.h)
class as a container for the patches and layers of fibers defined by users.
This class provides an array of _Fiber_ objects to the constructors of the
fiber section classes. Each _Fiber_ object contains a _UniaxialMaterial_
(or _NDMaterial_) and the fiber's area and section coordinates. After a
fiber section gets the material pointers, areas, and coordinates for all of
its fibers, the _Fiber_ objects are tossed out.

However, if you take a look at the [_Fiber_](https://github.com/OpenSees/OpenSees/blob/master/SRC/material/section/fiber/Fiber.h) class interface, you'll see more
than simple storage and retrieval functionality. There are methods to set the
fiber strain from section deformations and to get the fiber stress resultant
and stiffness contributions.

Those fiber state determination methods are leftover from my attempt to
consolidate the different fiber section classes into one _FiberSection_ class.
Instead of multiple fiber section classes, _FiberSection2d_, _FiberSection3d_,
_NDFiberSection2d_, _NDFiberSection3d_, etc., all of which do the same thing
(sum section force and stiffness contributions from fibers), we could put the
_Fiber_ objects to use and have one _FiberSection_ class. 

As you might expect, the computational overhead that accumulates due to the
extra level of indirection for fiber state determination far outweighed the
improved software engineering. The FiberSection has to go through Fiber
objects to get the stress-strain response whereas, e.g., _FiberSection2d_, etc.
go directly to the stress-strain response. Using Fiber objects, each fiber
state determination requires two polymorphic calls instead of one. To make along story short, the _FiberSection_ class quickly moved into the OpenSees
retirement home. [Take a look](https://github.com/OpenSees/OpenSees/blob/master/SRC/material/section/FiberSection.cpp), it spits out errors with the old school
`g3ErrorHandler`.

Additional indirection is extremely beneficial at the higher levels of the
OpenSees framework where the indirection is computationally inconsequential,
e.g., with the _FE_Element_ and _DOF_Group_ classes. But this is not the case
at lower levels, and fibers are at the lowest level in the hierarchy of
nonlinear finite element analysis.

It's one of those 80-20 rules: 80% of the computations come from 20% of the
code. With fiber sections, it's more like 95-5. So you'll want to use as few
fibers as possible in each section. See [Kostic and Filippou (2012)](https://doi.org/10.1061/(ASCE)ST.1943-541X.0000501) for more information on
how many fibers to use.

Eventually, the [_SectionIntegration_](https://github.com/OpenSees/OpenSees/blob/master/SRC/material/section/integration/SectionIntegration.h) interface came along. Implementations of
_SectionIntegration_ encapsulate fiber areas and section coordinates in
specific arrangements--wide flange steel shapes, doubly reinforced concrete
sections, tube sections, etc. Users don't have to define multiple patches and
layers all the time for common section geometries and it became easy to use
section dimensions as parameters for reliability, sensitivity, and
optimization applications. This made me feel a lot better about my failed
consolidation of the fiber section classes.