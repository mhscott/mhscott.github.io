---
title: Non-Convergence Does Not Mean OpenSees Crashed
category: Nonlinear Analysis
---

I once had the following conversation with a concerned user (CU) of OpenSees:

> CU: "I was running a response history analysis, then all of a sudden OpenSees crashed."
> 
> PD: "Really, it crashed? Did you get the blue screen of death?"
> 
> CU: "No. The analysis stopped and I saw 'Failed to converge'. It crashed."
> 
> PD: "Oh, I see. What did the convergence test show on the last time step?"

The conversation continued until we figured out CU should opt for the relative simplicity of _Concrete02_ over _Concrete23_ in their 
fiber sections. Another CU claimed OpenSees crashed, only to find out that increasing the maximum number of iterations in the convergence 
test will give _ModifiedNewton_ a fighting chance to find equilibrium.

Nonlinear structural analysis is a dark art and OpenSees is not a magic spell. OpenSees can crash for several reasons: dereferencing an 
invalid or null pointer, memory leaks, unhandled exceptions, etc. It's the usual suspects for code written by humans in a programming 
language (C++) that does not do its own garbage collection.

A crash is a failure of resource management while non-convergence is a failure of the analysis algorithms or the finite element model. 
Note that non-convergence does not necessarily mean the structural model entered a failed or collapsed state. More on that in another post.

Below is a screenshot of non-convergence. The model response is nonlinear but the ModifiedNewton algorithm has only 10 iterations to converge. 
The norm is decreasing, but we need (a lot) more iterations to reach equilibrium. A smaller time step would help.

![Example of non-convergence in an OpenSees analysis](/assets/images/NonConvergence.png)

And here is a screenshot of a crash resulting in a core dump. I'd rather not say how I did this, but has Commander La Forge returned from 
shore leave yet?

![Example of core dump from an OpenSees analysis](/assets/images/CoreDump.png)

As a general rule, if you get back to the `OpenSees >` prompt, like in the first screenshot, OpenSees did not crash. Check the norms coming 
out of the convergence test, then play with the analysis options and time step.

If that doesn't help, check the material models and their input parameters--nine times out of ten, they are the culprits of non-convergence. 
Try swapping out one material model at a time for its elastic equivalent until you get to an analysis that converges. Diagnose the issue, 
then reintroduce the nonlinearity.

If you back all the way up to a completely linear-elastic model and still have convergence problems, delete your scripts, reboot your computer, 
and start over from scratch.

If it really was a crash, send me as much information as possible about the circumstances and how I can reproduce it.
