---
title: Be More Specific than "Fiber Element"
category: Element Formulations
---

I hear the term "fiber element" a lot, as in "We used fiber elements to model the response of this reinforced concrete moment frame". Usually, when people say something like this, they are referring to "force-based distributed plasticity frame elements with fiber-discretized cross sections". 

That is a mouthful. I can see the desire to shorten the description to "fiber element". But let's not shorten it so much that the meaning becomes ambiguous.

After all, displacement-based distributed plasticity frame elements can use fiber sections, as can frame elements with a mixed formulation and all the variations in between. The element formulation makes a big difference in the simulated results.

You can even use fiber sections in a zero length element to perform standalone moment-curvature and axial-moment interaction analyses. But that's beside the point--no one is going to say "fiber element" to refer to these use cases.

More importantly, saying "fiber element" to refer to the "force-based distributed plasticity ..." mouthful implies some sort of exclusive relationship between force-based elements and fiber sections. In fact, it's an open relationship and force-based elements see other people all the time. Force-based elements work just as well with stress resultant section models.

So, let's stop using the term "fiber element" to refer to a particular element formulation with a particular type of section constitutive model. I don't expect the entire mouthful--I would be happy if people said "force-based fiber element".
