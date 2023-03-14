---
title: Gimme All Your Modal Damping
category: Structural Dynamics
---

The [GimmeMCK integrator]({%post_url 2020-05-17-gimme-all-your-damping-all-your-mass-and-stiffness-too %}) is one of my more useful 
contributions to OpenSees. This integrator allows you to extract the individual mass, 
damping, and stiffness matrices, or some linear combination therein, in order 
to see what's assembled in an OpenSees model or to bootstrap new functionality.

While getting the mass and stiffness matrices seems to work, `GimmeMCK` only worked for Rayleigh and viscous damping (you know, actual 
damper elements), but not modal damping. I initially thought the issue was how modal damping is computed, but it turns out a simple function, 
`getCFactor()`, was not defined in the _GimmeMCK_ class. The fix was so easy I did a 
[web edit](https://github.com/OpenSees/OpenSees/commit/d2250047f74ab47fe7136e22769fcfb2e8b7c5a3) on GitHub instead of a branch and pull request.

Before figuring out the issue was a simple fix, I wrote the Python script below to construct the modal damping matrix from eigenvectors, 
assuming only nodal mass. Try the script on your modal damping models and verify that you get the same result as `ops.integrator('GimmeMCK',0,1,0)`.

```python
import openseespy.opensees as ops
import numpy as np

#
# Define your model
#

modalDamping = [0.05,0.1,0.1] ;# Or whatever
Nmodes = len(modalDamping)
w2 = ops.eigen(Nmodes)
N = ops.systemSize()
C = np.zeros((N,N))
for n in range(Nmodes):
    zeta = modalDamping[n]
    wn = w2[n]**0.5
    phin = np.zeros(N)
    for nd in ops.getNodeTags():
        mass = ops.nodeMass(nd)
        for i,dof in enumerate(ops.nodeDOFs(nd)):
            if dof < 0:
                continue
            phin[dof] = mass[i]*ops.nodeEigenvector(nd,n+1)[i]
    C = C + 2*zeta*wn*np.outer(phin,phin)
```
