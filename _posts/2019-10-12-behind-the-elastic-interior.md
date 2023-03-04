---
title: Behind the Elastic Interior
category: Element Formulations
---

If VH1 expanded its list of the top one hit wonders of the 2000s to include journal articles in structural engineering, 
[Scott and Fenves (2006)](https://doi.org/10.1061/(ASCE)0733-9445(2006)132:2(244)) would be right up there with James Blunt 
and Gnarls Barkley. Actually, Google Scholar puts the article up there with some [nice company](https://scholar.google.com/citations?view_op=list_classic_articles&hl=en&by=2006&vq=eng_structuralengineering).

Like all journal articles, there's something you wish you had done differently--an explanation, a derivation, a figure, an example, etc. 
This article had a shortcoming in the explanation of the element interior.

The article says that the element interior, away from the plastic hinge regions, is linear-elastic. We even showed the compatibility 
relationship over the interior as a closed-form integral. Unfortunately, this came across as the element interior has to be linear-elastic, 
as if it is a restriction or limitation of the formulation. 

Understandable. But, in another nod to VH1, let's take a look behind the interior.

Closed-form integrals are cumbersome and limiting compared to numerical integration.  You can map two Gauss points on to the interior 
region and assign linear-elastic section response at these two points to get the same result as the closed-form integral. Use three if 
you want a point at or near midspan.

Taking this one step further, you can assign nonlinear constitutive response, e.g., fiber sections, to the integration points on the element 
interior (and to the two other points in the modified Gauss-Radau hinge integration approach). BOOM, you have distributed plasticity with the 
plastic hinge lengths you want as integration weights at the element ends!

But there's no silver bullet--among other issues, you will end up with negative integration weights when the plastic hinge lengths meet certain 
conditions.

At any rate, the OpenSees implementation of the _BeamWithHinges_ element existed for only a short time separate from the _NonlinearBeamColumn_ 
element. By the time Scott and Fenves (2006) was published, these distributed plasticity force-based formulations were combined in to one 
class, _ForceBeamColumn_. The only difference is the _BeamIntegration_ object they use at run-time.

We also changed the [`beamWithHinges`](https://opensees.berkeley.edu/wiki/index.php/Beam_With_Hinges_Element) element command, the one where 
you have to specify _E_, _A_, and _I_ for the interior, because it reinforced 
the idea that the interior had to be linear-elastic. All we did with those elastic constants was create _ElasticSection_ objects to pass in to 
the plastic hinge _BeamIntegration_ object. The preferred element command takes a section tag instead of _E_, _A_, and _I_, but we maintain the 
original command for backward compatibility.

Writing about the elastic interior sooner could have saved me some frustration.  I still come across people who believe the interior has 
to be elastic. I try to politely explain that's not the case.

I don't consider this implementation issue to be an LPU, so this blog post will have to suffice. After all, no one listens to the second 
song from a one hit wonder, but they might listen to the remix.
