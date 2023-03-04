---
title: Torsion with Fiber Sections
category: Constitutive Models
---

I won't blog about every pull request to [OpenSees on GitHub](https://github.com/OpenSees/OpenSees), but I will blog about pull 
requests that could affect backward compatibility of user scripts. [Pull request #142](https://github.com/OpenSees/OpenSees/pull/142) 
is one such case. It affects how torsion is added to fiber sections in three-dimensional models.

The frame elements require torsional stiffness in order to prevent a local rigid body mode of spinning. The fibers in a uniaxial fiber
section do not provide shear stress, so torsion is not one of the stress resultants. Torsion has to be added to the fiber section.

There are two methods to add torsion to a _FiberSection3d_ object: 1) use the _SectionAggregator_ to attach a _UniaxialMaterial_; 
2) use the `-GJ` option in the fiber section definition.

```tcl
# Method 1
section Fiber 3 {
   patch ...
   layer ...
}
uniaxialMaterial Elastic 5 $GJ ;# Doesn't have to be elastic
section Aggregator 1 5 T -section 3

# Method 2
section Fiber 1 -GJ $GJ {
   patch ...
   layer ...
}
```

Most people use Method 2 for which this pull request will not break backward compatibility. If you use Method 1, read on.

Years ago when I implemented Method 2, I put a bogus _GJ_ value in to the _FiberSection3d_ class to accommodate folks who might 
forget to use either Method 1 or Method 2. The force-based element would always have a full rank flexibility matrix to invert 
and the displacement-based element would have something to interpolate. No harm, no foul, right?

Wrong! This messed up folks who used Method 1, which apparently was a small group because it took years for anyone to notice this 
issue. It's likely they struggled in silence with OpenSees convergence problems just like everyone else.

The issue with Method 1 is that the _FiberSection3d_ object is created with an artificially high _GJ_ value, giving a section stiffness 
matrix with the following topology:

$${\bf k}_s = \left[
\begin{array}{cccc} 
X & X & X & 0 \\ 
X & X & X & 0 \\ 
X & X & X & 0 \\ 
0 & 0 & 0 & 1e14 
\end{array} 
\right]
$$

The 3x3 block is the axial-flexural stiffness that comes from the uniaxial fibers. With Method 2, the absurdly large 1e14 is overwritten 
with the `-GJ` option and everything is fine. But, with Method 1, when the _SectionAggregator_ adds the torsional _UniaxialMaterial_ 
to the fiber section, the section stiffness matrix ends up with two torsional modes--one that's junk and the other that the user requests:

$${\bf k}_s = \left[ 
\begin{array}{ccccc} 
X & X & X & 0 & 0 \\ 
X & X & X & 0 & 0 \\ 
X & X & X & 0 & 0 \\ 
0 & 0 & 0 & 1e14 & 0 \\ 
0 & 0 & 0 & 0 & GJ 
\end{array} 
\right]
$$

The frame elements in OpenSees are able to handle two torsional modes in the section--that's not the problem. The problem is that the
conditioning of the element stiffness matrix and/or stability of the element state determination could be poor due to the extremely 
stiff torsional response, leading to global convergence issues. What was I thinking?

This pull request fixes the issue by forcing the user to specify either a _GJ_ value or a _UniaxialMaterial_ for torsion when defining 
the fiber section.

```tcl
# New Method 1
uniaxialMaterial Elastic 5 $GJ ;# Doesn't have to be elastic
section Fiber 1 -torsion 5 {
   patch ...
   layer
}

# Method 2 (same as before)
section Fiber 1 -GJ $GJ {
   patch ...
   layer ...
}
```

With the new Method 1, the _FiberSection3d_ class is the aggregator, not the aggregated. Unfortunately, backwards compatibility is broken 
for the old Method 1. After this pull request, you will receive an error stating that there is no torsion in your fiber section. You have 
to use either the `-GJ` or the `-torsion` option.

The _SectionAggregator_ did not go anywhere. You can still aggregate shear to a fiber section and create other aggregations of section 
response without any problems.
