---
title: Results of a New Challenge
category: Community
---

After proposing a [modeling challenge]({% post_url 2020-03-01-a-semi-blind-kind-of-contest %})
for linear-elastic analysis of a strongback frame, I proposed a
[second challenge]({% post_url 2020-05-04-a-new-challenge %}) for linear-elastic analysis of
the Ziemian frame. There were eight participants in this challenge, an
increase from five for the first challenge.

Due to the light gravity loads on the frame, whether or not the analysis
included self-weight made a significant difference in the requested roof
displacement. Of the eight participants, four also computed roof displacement
that accounted for the self-weight of the frame members.

The results are shown below, along with the median values among the
submissions--0.31 mm with self weight and 0.12 mm without. Temporarily
forsaking OpenSees, I analyzed the Ziemian frame in G2 and (fortunately)
obtained the median results. You can find the G2 input file
[here](https://github.com/mhscott/G2/blob/master/EXAMPLES/ziemian.m).

![Results from Ziemian frame modeling challenge](/assets/images/Ziemian.png)

Eyeball statistical analysis reveals some variance in the results, but not
enough to worry about. The variations are most likely due to small differences
in the input section and material properties.

In forthcoming challenges, we'll move beyond linear-elastic analysis with a
pushover analysis of the strongback frame and a push-down analysis of the
Ziemian frame. Also, nonlinear analysis of a reinforced concrete beam has been
suggested for a future challenge, but let's take it one step at a time.
Stay tuned!