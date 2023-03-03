---
layout: post
title: Be Careful with Modal Damping
author: Michael H. Scott
categories: ["Structural Dynamics"]
---

Modal damping is kind of the it-spell in the dark art that is modeling 
viscous damping in structures. Although modal damping is pretty 
straightforward, you should be aware of an important aspect of its 
implementation in OpenSees.

The issue, which is described in section 9 of this paper, is that 
OpenSees assembles the dynamic tangent into the matrix storage scheme 
you choose via the `system` command. This is a problem for modal damping 
because the damping matrix is full even when the matrix storage, based 
on the nodal connectivity, is banded, profile, or sparse. As a result, 
some terms of the dynamic tangent will be zero even though they are 
non-zero in the modal damping matrix. Convergence problems will 
ensue--even for linear problems--because the dynamic tangent will be 
inconsistent with the residual.

Let's describe this issue with a canonical shear frame model.

IMAGE

Using floor mass *m*=1.0352 and story stiffness *k*=610, the 
model has the following mass and stiffness matrices

$$
{\bf M}=\left[ 
\begin{array}{cccc} 
1.0352 & 0 & 0 & 0 \\ 
0 & 1.0352 & 0 &  0 \\ 
0 & 0 & 1.0352 & 0 \\ 
0 & 0 & 0 & 0.5176 
\end{array}\right] 
\hspace{0.1in} 
{\bf K}=\left[ 
\begin{array}{cccc} 
1220 & 0 & -610 & 0 \\ 
0 & 1220 & -610 & -610 \\ 
-610 & -610 & 1220 & 0 \\ 
0 & -610 & 0 & 610 
\end{array}\right]
$$

Note that the equations are numbered 1-3-2-4 (use the `Plain` equation 
numberer) so that the stiffness matrix is not tridiagonal--its bandwidth 
is 3, the profile dips inside the band, and there's sparsity within the 
profile. Accordingly, the `BandSPD`, `ProfileSPD`, and `UmfPack` systems 
will store different numbers of non-zeros.

Now suppose we want to use 5% damping ($$\zeta$$=0.05) in the first 
mode. Based on the mass and stiffness, the first mode frequency is 
$$\omega_1$$=9.4715 rad/sec and the corresponding eigenvector and 
the resulting modal damping matrix are



```tcl
#
# Free vibration analysis of shear frame with modal damping
# Using zero length elements for the model
#

wipe
model basic -ndm 1 -ndf 1

set k 610.0 ;# Story stiffness
set m 1.0352 ;# Floor mass

node 0 0.0; fix 0 1
node 1 0.0
node 2 0.0
node 3 0.0
node 4 0.0

mass 1 $m
mass 2 $m
mass 3 $m
mass 4 [expr 0.5*$m]

uniaxialMaterial Elastic 1 $k

element zeroLength 1 0 1 -mat 1 -dir 1
element zeroLength 2 1 2 -mat 1 -dir 1
element zeroLength 3 2 3 -mat 1 -dir 1
element zeroLength 4 3 4 -mat 1 -dir 1

# Impose displacement at roof
timeSeries Constant 1
pattern Plain 1 1 {
    sp 4 1 1.0
}

# Do a static analysis for initial condition
integrator LoadControl 0.0
constraints Transformation
analysis Static

analyze 1

wipeAnalysis

# Remove displacement so it doesn't affect eigen calculations
remove loadPattern 1

set N 2 ;# Number of modes for modal damping
eigen $N

modalDamping 0.05 0.02 ;# 5% in mode 1, 2% in mode 2

# Switch to dynamic analysis
analysis Transient

# Record roof displacement
recorder Node -file roofDisp.out -time -node 4 -dof 1 disp

# Analysis time, step, and num steps
set Tf 5.0
set dt 0.01
set Nsteps [expr int($Tf/$dt)]

analyze $Nsteps $dt
```
