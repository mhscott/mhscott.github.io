---
title: Start with the Correct Static Resisting Force
category: Nonlinear Analysis
---

Thanks to my involvement with OpenSees, I have been asked to review several
manuscripts on nonlinear dynamic structural analysis. It's always wonderful to
see what other researchers are up to and how they use OpenSees or other
software to achieve their research objectives.

A brief overview of the governing dynamic equilibrium equation, i.e., the
equation of motion, is standard in the manuscripts I've reviewed--and in this
post. For the common case of linear damping and inertial forces, nonlinearity
comes from only the static resisting force vector, $${\bf P}_r$$, which is
expressed as a function of the displacement vector, $${\bf U}$$:

$${\bf M}\ddot{\bf U} + {\bf C}\dot{\bf U} + {\bf P}_r({\bf U}) = {\bf P}(t) \mbox{ (CORRECT)}$$

Unfortunately, a few manuscripts I've reviewed get the equation of motion
wrong by expressing the static resisting force vector as
$${\bf K}_T{\bf U}$$, the product of the tangent stiffness matrix,
$${\bf K}_T=\partial{\bf P}_r/\partial{\bf U}$$, with the displacement vector:

$${\bf M}\ddot{\bf U} + {\bf C}\dot{\bf U} + {\bf K}_T{\bf U} = {\bf P}(t) \mbox{ (INCORRECT)}$$

This equation tries to fit a changing stiffness matrix into the framework of
linear dynamic structural analysis. Yes, nonlinear analysis can be viewed as a
sequence of linear analyses, but not in that way. The difference between these
two expressions for the static resisting force vector can be significant.

Consider case (a) in the figure below, where the static resisting force is in
a state of loading. The solid circle is the correct static resisting force,
$${\bf P}_r({\bf U})$$, while the incorrect $${\bf K}_T{\bf U}$$ is the height
of a triangle made with displacement as its base and the tangent stiffness
(moved to the origin) as its slope. The incorrect static resisting force is
much smaller than the correct value.

![Interpretations of static resisting force](/assets/images/ResistingForce.png)

Now consider case (b), where the static resisting force is unloading with its
initial stiffness. After forming a triangle from the displacement and the
tangent stiffness at the origin, the incorrect $${\bf K}_T{\bf U}$$ is
much larger than the correct value. Furthermore, as unloading continues,
$${\bf P}_r({\bf U})$$ and $${\bf K}_T{\bf U}$$ will have different signs.

The figure is a single degree of freedom representation of a multi-dimensional
problem. So, instead of magnitudes, signs, and triangles, let visions of
norms, vectors, and hyperplanes dance in your head.

Although they use software that computes the correct static resisting force,
it's possible the authors don't realize their error. If I see the incorrect
equation of motion when reviewing a manuscript, my review decision is
easy--I try to be constructive and not too snarky in my review comments.
But would you trust the interpretation of results made by authors who do not
express the equation of motion correctly?
