---
title: Be Careful with Modal Damping
category: Structural Dynamics
---

Modal damping is kind of the it-spell in the dark art that is modeling 
viscous damping in structures. Although modal damping is pretty 
straightforward, you should be aware of an important aspect of its 
implementation in OpenSees.

The issue, which is described in section 9 of [Chopra and McKenna (2015)](https://doi.org/10.1002/eqe.2622), is that 
OpenSees assembles the dynamic tangent into the matrix storage scheme 
you choose via the `system` command. This is a problem for modal damping 
because the damping matrix is full even when the matrix storage, based 
on the nodal connectivity, is banded, profile, or sparse. As a result, 
some terms of the dynamic tangent will be zero even though they are 
non-zero in the modal damping matrix. Convergence problems will 
ensue--even for linear problems--because the dynamic tangent will be 
inconsistent with the residual.

Let's describe this issue with a canonical shear frame model.

![Four story shear frame model](/assets/images/ShearFrame.png)

Using floor mass *m*=1.0352 and story stiffness *k*=610, the 
model has the following mass and stiffness matrices

$$
{\bf M}=\left[ \begin{array}{cccc} 
1.0352 & 0 & 0 & 0 \\ 
0 & 1.0352 & 0 &  0 \\ 
0 & 0 & 1.0352 & 0 \\ 
0 & 0 & 0 & 0.5176 
\end{array}\right] 
\hspace{0.1in} 
{\bf K}=\left[ \begin{array}{cccc} 
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

$$
\phi_1=\left[ \begin{array}{c}
0.26596 \\ 0.64208 \\ 0.49143 \\  0.69498
\end{array} \right]
\hspace{0.1in}
{\bf C}=2\zeta\omega_1({\bf M}\phi_1)({\bf M}\phi_1)^T=
\left[ \begin{array}{cccc}
0.07179465 & 0.17332762 & 0.13265922 & 0.09380423 \\
0.17332762 & 0.4184499 & 0.32026769 & 0.22646345 \\
0.13265922 & 0.32026769 & 0.24512228 & 0.17332762 \\
0.09380423 & 0.22646345 & 0.17332762 & 0.12256114
\end{array} \right]
$$

(Update April 11, 2022: corrected modal damping matrix to include mass matrix)

Assuming Newmark time integration with constant average acceleration ($$\gamma=0.5$$ and $$\beta=0.25$$) and time step $$\Delta t=0.01$$ sec, the consistent dynamic tangent is

$${\bf K}_T = {\bf K} + \frac{\gamma}{\beta \Delta t}{\bf C} + \frac{1}{\beta(\Delta t)^2}{\bf M} =
\left[ \begin{array}{cccc}
4.26423589e+04 & 3.46655246e+01 & -5.83468156e+02 & 1.87608467e+01 \\
3.46655246e+01 & 4.27116900e+04 & -5.45946463e+02 & -5.64707309e+02 \\
-5.83468156e+02 & -5.45946463e+02 & 4.26770245e+04 & 3.46655246e+01 \\
1.87608467e+01 & -5.64707309e+02 & 3.46655246e+01 & 2.13385122e+04
\end{array} \right]
$$

With banded storage, the dynamic tangent that OpenSees will use in the analysis is

$$
{\bf K}_T =
\left[ \begin{array}{cccc}
4.26423589e+04 & 3.46655246e+01 & -5.83468156e+02 & 0 \\
3.46655246e+01 & 4.27116900e+04 & -5.45946463e+02 & -5.64707309e+02 \\
-5.83468156e+02 & -5.45946463e+02 & 4.26770245e+04 & 3.46655246e+01 \\  0 &
-5.64707309e+02 & 3.46655246e+01 & 2.13385122e+04
\end{array} \right]
$$

Notice the zeros at the upper right and lower left corners of the matrix, outside the bandwidth of 3. Because of these zeros, the dynamic tangent used in the analysis will be inconsistent with the residual and your analyses won't converge in one iteration for linear problems.

Consider the banded matrix solver `BandSPD` in a free vibration analysis of the shear frame. The analysis takes four iterations to find equilibrium at each time step.

![Modal damping example with BandSPD solver](/assets/images/BandSPD.png)

Although not shown here, the analysis will take three or four iterations per time step if you switch the solver to `ProfileSPD` or `UmfPack`, or any solver that does not store the full matrix.

Because it stores the entire matrix, you can use the `FullGeneral` solver to get the convergence in one iteration that you expect for a linear problem.

![Modal damping example with FullGeneral solver](/assets/images/FullGeneral.png)

Don't worry though, the analyses will converge to the same solution regardless of which solver you use. However, without getting into spectral radii, it's possible that the analysis will not converge at all due to inconsistent zeros in the dynamic tangent. But this is unlikely for realistic values of mass, stiffness, and damping.

For large models, using a solver with full matrix storage will be dreadfully slow. You'll be better off taking the extra iterations with a fast sparse solver like `UmfPack` than using the `FullGeneral` solver to converge in one iteration. When using a fast sparse solver, don't use the `NewtonRaphson` algorithm--you don't want to factorize the same inconsistent tangent over and over. Consider `KrylovNewton` or `BFGS` instead. And don't use the `Linear` algorithm because it won't iterate to equilibrium.

Recognizing that one size does not fit all, OpenSees was designed to give you total control over the analysis. Therefore, it's important you are aware of issues like this one with modal damping so that you can choose analysis options wisely.

---
<br>
Here is an OpenSees Tcl script of the foregoing analysis.

```tcl
# Free vibration analysis of shear frame with modal damping
# Using zero length elements for the model

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
element zeroLength 2 1 3 -mat 1 -dir 1
element zeroLength 3 3 2 -mat 1 -dir 1
element zeroLength 4 2 4 -mat 1 -dir 1

# Impose displacement at roof
timeSeries Constant 1
pattern Plain 1 1 {
    sp 4 1 1.0
}

# Do a static analysis for initial condition
integrator LoadControl 0.0
numberer Plain
constraints Transformation
analysis Static

analyze 1

wipeAnalysis

# Remove displacement so it doesn't affect eigen calculations
remove loadPattern 1

set N 1 ;# Number of modes for modal damping
eigen $N

modalDamping 0.05 ;# 5% in mode 1

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
