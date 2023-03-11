---
title: A New Challenge
category: Community
---

The results from a previous
[modeling challenge]({% post_url 2020-03-01-a-semi-blind-kind-of-contest %})
were excellent with 100% of entries correct.
But before we deep dive into the dark world of modeling nonlinear structural response, let's do
one more challenge with linear analysis.

The frame model shown below is UP50HA from a series of low-rise industrial structures whose
reliability under gravity loading was quantified by
[Buonopane and Schafer (2006)](https://doi.org/10.1061/(ASCE)0733-9445(2006)132:2(267)).
The frame, now commonly known as the "Ziemian Frame", was the centerpiece of Ron Ziemian's Ph.D.
dissertation and is a good example for modeling combined material and geometric nonlinearity.
Before going to nonlinear response, let’s examine the linear response.

![Ziemian frame model and loads](/assets/images/IndustrialFrame.png)

All joints are rigid and infinitesimal and the supports are pin boundary conditions. All members
are steel with _E_=200,000 MPa and are oriented for strong axis bending. Due to its lack of
symmetry, the frame will displace horizontally under the vertical loads shown.

The distributed loads on the girders are low, about 30 kN total, keeping the frame response
within the linear-elastic, small displacement range. So, there's no need to use a corotational
mesh, as described in the paper, at least not yet.

What is the horizontal displacement (units=mm) of the joint indicated in the figure?

E-mail me your answer. You don't have to use OpenSees–use whatever software you would like.
Let me know if you have any questions.

I look forward to your responses. I will share anonymized results in another post. Future
challenges will focus on nonlinear structural response of this model and the strongback model
from the previous challenge.

---
<br>
Update (May 5, 2020): Due to the low magnitude of the distributed loads, including self-weight
of the frame members will make a significant difference in the computed response.

---
<br>
RESULTS