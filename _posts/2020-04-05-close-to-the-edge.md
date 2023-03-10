---
title: Close to the Edge
category: Constitutive Models
---

A [colleague](https://www.ericafischer.org/) in Eastchester 
[tweeted](https://twitter.com/erica_fischer/status/1246461723030081536) this graphic, created by 
[Stephen Ressler](https://stephenjressler.com/). It's a fun interpretation of social distancing, 
but a little abstract compared to other, more concrete, guidelines going around. For example, in Florida, the rule of thumb
is to maintain a distance equal to an adult alligator length. You can visualize an alligator, but you can't see a normal stress.

![Social distancing](/assets/images/SocialDistancing.png)

Naturally, I thought it would be a good example to use in class. Before doing so, I verified the normal stress calculation, 
$$\sigma$$=_M/S_. From the 15th edition AISC Steel Manual, the section modulus for a W12x14 is _S_=14.9 in<sup>3</sup>. 
And, from statics, the maximum bending moment for a simple span with uniform distributed load is 
_M=wL<sup>2</sup>/8_, which works out to 108 kip-in.

The normal stress is then 108/14.9=7.25 ksi, not the 7.26 ksi shown in the graphic. I suppose one could get 7.26 ksi with a 
more precise value for _S_. Although I spent the better part of a day agonizing over this discrepancy and significant figures, 
that's not the point of this post.

The point of this post is to show that using a fiber section model in OpenSees, you'll be much further than 0.01 ksi away from 
the expected maximum normal stress. Assuming you use three rectangular patches defined by the W12x14 section depth (11.9 inch), 
web thickness (0.200 inch), flange width (3.97 inch), and flange thickness (0.225 inch) from the Steel Manual, here's why.

With midpoint integration of fiber stresses, the section will never sample stress-strain response at the outer faces of the flanges. 
The sampling will always be half a fiber thickness away.

![Fiber discretization of wide-flange section](/assets/images/WFSection.png)

As the number of fibers increases, the estimate of _S_ improves and the centroid of the extreme fiber moves closer to the edge. 
The maximum normal stress computed via the fiber section converges; however, it converges to 7.48 ksi instead of the expected 
7.25 ksi. This discrepancy arises because the section modulus, _S_, tabulated in the Steel Manual accounts for fillet rounds between 
the web and flanges--there's more steel area and thus lower bending stress than the fiber section.

![Maximum normal stress computed with increasing number of fibers](/assets/images/FiberW12x14.png)

I suppose I could code up Gauss-Lobatto integration through the depth of fiber patches in order to sample stress-strain response
at the edges. I could also code up Delaunay Triangulation for the fiber section discretization in order to account for fillet rounds. 
Neither of these endeavors is worthwhile; however, the longer social distancing continues, the more likely it is you will see 
these pull requests.
