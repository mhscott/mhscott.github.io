---
title: Algorithmic Limerick
category: Nonlinear Analysis
---

> There once was a post-doc named Dave,
>
> Who ran OpenSees in his cave.
>
> Non-convergence, 'bout to miss dinner,
>
> He switched the algorithm to 'Linear'.
>
> Just think of all the trouble he saved.

Although I changed the name and circumstances to protect the guilty, I have
known more than one person who, when faced with convergence problems in
OpenSees, decided to use the `Linear` algorithm.

They were excited to see their analysis rocket through all time steps, but
that excitement quickly wore off when they looked at the results.

The Linear algorithm in OpenSees is an explicit algorithm--there is no
iteration to equilibrium. The algorithm simply forms the current tangent and
residual, solves for the displacement increments, updates the domain, then
tells the integrator it's ready to move on to the next time step. It's like
using the `Newton` algorithm with `iter` set to 1 and `pFlag` set to 5 in the
[convergence test](https://opensees.berkeley.edu/wiki/index.php/Norm_Unbalance_Test).
Strange model states can be committed as part of the
solution path, especially when there is a load reversal.

Unless you're running fast hybrid simulations or using super small time steps,
e.g., $$\Delta t=10^{-6}$$ sec, with an explicit integrator, don't use the
`Linear` algorithm.

Even if you think your model is linear, it's still a good idea to use an
implicit algorithm, e.g., `Newton`, to make sure there's no
nonlinearity or inconsistent tangents lurking in a hidden cave of your model.
