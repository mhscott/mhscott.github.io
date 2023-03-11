---
title: Gimme All Your Damping, All Your Mass and Stiffness Too
category: Structural Dynamics
---

Just because OpenSees is open source does not mean it is a fully transparent box. This is mostly because documentation has lagged behind development. So, pessimists would say the box is semi-opaque while optimists would characterize it as semi-transparent. But a few parts of OpenSees are definitely housed in an opaque box.

Take, for instance, the stiffness matrix. We received many requests for functionality to extract the stiffness matrix for post-processing and offline calculations, e.g., in MATLAB. As a result, the `printA` function was added a few years ago. This function returns the left-hand side matrix in whatever linear system of equations, $${\bf A}{\bf x}={\bf b}$$, OpenSees currently has in memory.

Note that the `printA` function only works with the `FullGeneral` system of equations, which stores the entire _N x N_ matrix. Getting the non-zeros out of sparse matrix storage and filling in zeros where needed is just not worth the effort.

With no input arguments, `printA` prints the **A** matrix to the standard output stream, which is useless for any model with more than two DOFs. There is, however, an option to write the matrix to a file with `printA -file filename`. You can then load this file into MATLAB. In OpenSeesPy, there is an option to return the matrix as a list to the interpreter, `printA('-ret')`. More on this later.

For a static analysis, `printA` gives the tangent stiffness matrix. For a dynamic analysis, `printA` gives the effective tangent stiffness matrix, which is not physically meaningful because it is a linear combination of the mass, damping, and static stiffness matrices with scalar coefficients dictated by the integrator and the time step.

Let's suppose you're doing a dynamic analysis and want to get **M**, **C**, and **K** from your model. You can get **K** by first doing a static analysis, then calling `printA`. Easy. And, I recently learned that you can get **M** by calling `printA` after switching to the `NewmarkExplicit` integrator with $$\gamma$$=0. Also easy. If you are using Rayleigh damping where **C** is a linear combination of **M** and **K**, you're done.

But what if your model has viscous dampers and you want to get the non-classical damping matrix? What if you want to do state space calculations offline because OpenSees does not have state space functionality? You'll be SOL on the damping matrix. As far as I can tell, there's no integrator available that you can trick into forming only the damping matrix like you can with `NewmarkExplicit` and the mass matrix.

So, after trying some arduous and non-scalable approaches to forming **C** with imposed velocities and single point constraints, I realized I could just implement a new integrator to trick OpenSees into giving me what I want. This new integrator assembles any linear combination of mass, damping, and stiffness (tangent and initial) that you tell it. Then, you can grab that matrix with `printA`. I named it `GimmeMCK` (see [PR #343](https://github.com/OpenSees/OpenSees/pull/343)) because better names just didn't come to mind.

Python
```python
ops.integrator('GimmeMCK', m, c, kt, ki=0.0)
```

Tcl
```tcl
integrator GimmeMCK $m $c $kt <$ki>
```

+ `m` = factor applied to mass matrix
+ `c` = factor applied to damping matrix
+ `kt` = factor applied to tangent stiffness matrix
+ `ki` = factor applied to initial stiffness matrix (optional, default=0.0)

Assembles `A = m*M + c*C + kt*KT + ki*KI`

Because you don't want to perform an actual analysis with this integrator,
I removed the functionality to advance time in the domain and to update
displacement, velocity, and acceleration like you see in the proper dynamic
integrators. Although, maybe one would want this functionality to play around
with new integrators without having to touch the source code. Let me know if
that functionality appeals to you.

With your model defined, at any time you can switch to the `GimmeMCK`
integrator, dump out the matrices you want, then switch back to a real dynamic
integrator and proceed as if nothing happened. Don't worry if you see analysis
failed warnings because the assembled matrix is singular, e.g., massless DOFs
when assembling **M** or small number of damping elements when
assembling **C**.

Below is a synopsis of using the `GimmeMCK` integrator to get the mass,
stiffness, and damping matrices from an OpenSees model defined in Python.

```python
import openseespy.opensees as ops
import numpy as np
import scipy.linalg as slin
 
#
# Define your model
#
 
ops.wipeAnalysis()
ops.system('FullGeneral')
ops.analysis('Transient')
 
# Mass
ops.integrator('GimmeMCK',1.0,0.0,0.0)
ops.analyze(1,0.0)
 
# Number of equations in the model
N = ops.systemSize() # Has to be done after analyze
 
M = ops.printA('-ret') # Or use ops.printA('-file','M.out')
M = np.array(M) # Convert the list to an array
M.shape = (N,N) # Make the array an NxN matrix
 
# Stiffness
ops.integrator('GimmeMCK',0.0,0.0,1.0)
ops.analyze(1,0.0)
K = ops.printA('-ret')
K = np.array(K)
K.shape = (N,N)
 
# Damping
ops.integrator('GimmeMCK',0.0,1.0,0.0)
ops.analyze(1,0.0)
C = ops.printA('-ret')
C = np.array(C)
C.shape = (N,N)
```

Continue with the following code if you want to perform state space analysis
on the model. You'll notice code to separate the DOFs with mass from those
without mass using the nodeDOFs and nodeMass commands. This separation is
necessary for static condensation prior to the state space analysis. You may
find the node utility commands useful for other stuff too.

```python
# Determine number of DOFs with mass
massDOFs = []
for nd in ops.getNodeTags():
    for j in range(NDF): # NDF is number of DOFs/node
        if ops.nodeMass(nd,j+1) > 0.0:
            massDOFs.append(ops.nodeDOFs(nd)[j])
 
# Number of DOFs with mass
Nmass = len(massDOFs)
 
# DOFs without mass
masslessDOFs = np.setdiff1d(range(N),massDOFs)
Nmassless = len(masslessDOFs)
 
# Form matrices for D*x = -lam*B*x
B = np.zeros((2*Nmass,2*Nmass)) # = [ 0 M; M C]
D = np.zeros((2*Nmass,2*Nmass)) # = [-M 0; 0 K]
 
# Mass
B[:Nmass,:][:,Nmass:2*Nmass] =  M[massDOFs,:][:,massDOFs]
B[Nmass:2*Nmass,:][:,:Nmass] =  M[massDOFs,:][:,massDOFs]
D[:Nmass,:][:,:Nmass]        = -M[massDOFs,:][:,massDOFs]
 
# Damping
B[Nmass:2*Nmass,:][:,Nmass:2*Nmass] = C[massDOFs,:][:,massDOFs]
 
# Static condensation
Kmm = K[massDOFs,:][:,massDOFs];     Kmn = K[massDOFs,:][:,masslessDOFs]
Knm = K[masslessDOFs,:][:,massDOFs]; Knn = K[masslessDOFs,:][:,masslessDOFs]
# Kc = Kmm - Kmn*inv(Knn)*Knm
if Nmassless > 0:
    Kc = Kmm - np.dot(Kmn,np.linalg.solve(Knn,Knm))
else:
    Kc = K
 
# Stiffness at DOFs with mass
D[Nmass:2*Nmass,:][:,Nmass:2*Nmass] = Kc
 
# State space eigenvalue analysis
[lam,x] = slin.eig(D,-B)
```

Let me know if you run into any problems getting the matrices out of OpenSees.

---
<br>
The title for this post was inspired by ZZ Top’s "Gimme All Your Lovin'",
which saw heavy play on MTV, back when MTV played music videos.

[![ZZ Top](http://img.youtube.com/vi/Ae829mFAGGE/0.jpg)](http://www.youtube.com/watch?v=Ae829mFAGGE "ZZ Top")
