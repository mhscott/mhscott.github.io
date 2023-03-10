---
title: Better Late than Never
category: Element Formulations
---

Frame finite elements abound in OpenSees, but something very useful has always been missing--a 
linear-elastic beam element with moment releases.

Sure, you can define two nodes at the release location and use the equalDOF command; however, 
that can be error prone and you're at the mercy of the constraint handler. On the positive side, 
this approach will work for material nonlinear element formulations.

However, a good, old fashioned linear-elastic beam element with moment releases can get you 
pretty far in nonlinear structural analysis. With a small amount of logic, you can script 
an event-to-event analysis in order to determine the pushover curve and collapse mechanism for a frame.

Even [G2](https://github.com/mhscott/G2) has such an element (`element3`). 
But not OpenSees, not until [PR #270](https://github.com/OpenSees/OpenSees/pull/270)
modified the `elasticBeamColumn` element (2D version only) to take a moment release code as an optional input. 
The releases will also work with uniform distributed loads and point loads as member loads on the element.

Check the moment releases out, give them a try. They may even come in handy for this 
[frame analysis challenge]({% post_url 2020-03-01-a-semi-blind-kind-of-contest %}).
