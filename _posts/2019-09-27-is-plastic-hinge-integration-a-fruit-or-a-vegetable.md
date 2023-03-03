---
layout: post
title: Is Plastic Hinge Integration a Fruit or a Vegetable?
category: Element Formulations
---


If distributed plasticity elements are fruits and concentrated plasticity elements are vegetables, then plastic hinge integration is something like a tomato. 

Based on papers I've read, manuscripts I've reviewed, presentations I've seen, etc., there's some confusion as to whether plastic hinge integration is a fruit or a vegetable. Here's my two cents.

Concentrated plasticity elements use moment-rotation springs at the ends of an elastic beam. The element moment-rotation response falls out of the three-springs-in-series configuration of the macro element. To prevent double counting of flexibility, the springs will have artificially high initial stiffness or rigid links will be added to the series configuration.

Distributed plasticity elements integrate moment-curvature relationships along the element length. The element moment-rotation response comes out of the integration. No need for rigid links or artificially high stiffness.

Plastic hinge integration, presented in [Scott and Fenves (2006)](https://doi.org/10.1061/(ASCE)0733-9445(2006)132:2(244)) and implemented as `beamWithHinges` in OpenSees, integrates moment-curvature relationships at the element ends over specified plastic hinge lengths. The integration over the interior, away from the plastic hinges, assumes linear-elastic moment-curvature response (but this need not be the case, something I'll address in another post). So, one could argue that plastic hinge integration is a concentrated plasticity formulation because all of the nonlinearity is at the element ends.

The difference is that the nonlinear moment-curvature response is integrated over a physical length within the element--not outside the element from a priori calibration or rigid links. Therefore, plastic hinge integration is really plasticity distributed over specified lengths. In other words, it's distributed plasticity.

Just like a tomato, even though it's often thought of as a vegetable, plastic hinge integration is most definitely a fruit.

And whatever you do, don't try to make moment-curvature in plastic hinge integration equal to moment-rotation in concentrated plasticity by setting the plastic hinge lengths equal to one. My review of your work will not be favorable.
