---
title: Not Just a Modeling Term
category: Community
---

"Master/slave" is common terminology to describe relationships in many technical fields, e.g., between tables in databases
or between devices in control systems. The terminology also appears in finite element analysis where the response of one
node controls the response of another node through constraints.

However, this terminology is based on archaic relationships within our society. As engineers, we have other ways of
describing constraints between nodes, ways that are actually more descriptive of the technical nature of the relationship.

While "primary/replica" is a suitable alternative for databases, "primary/secondary" and "retained/constrained"
are the most widely used alternatives for finite element models. The response of the retained (primary) node is kept in the global system
of governing equations while the response of the constrained (secondary) node is removed from the equations via a constraint handling method.

At the [request](https://twitter.com/pollybmurray/status/1271826812171980800) of a dedicated OpenSees user,
I endeavored to change all occurrences of "master/slave" to "retained/constrained"
in the [OpenSees wiki](https://opensees.berkeley.edu/wiki/index.php/OpenSees_User).
This change is aligned with not only the mathematical concepts that these words represent, but
also our profession's efforts to build an inclusive community.

In addition to the wiki documentation, the terminology will be changed in the comments, variable names, recorder options,
and other lines of the OpenSees source code. Example scripts hosted on the OpenSees GitHub site will be updated as well.

I encourage other software developers in engineering, whether the software is for commercial, instructional, or research
purposes, to stop using this terminology and to help build an inclusive professional community.

For more information on why we should stop using the "master/slave" terminology, read
[this post](https://medium.com/@mikebroberts/let-s-stop-saying-master-slave-10f1d1bf34df).

A [colleague](https://www.ericafischer.org/) in Eastchester provided constructive feedback prior to publishing this post.

---
<br>
Update, July 5, 2020: OpenSees GitHub [PR #381](https://github.com/OpenSees/OpenSees/pull/381),
changed "master/slave" terminology in `SRC/` and `EXAMPLES/` to "primary/secondary"
and "retained/constrained". A few occurrences of the previous terminology
are in third party code (Tcl/Tk and AMGCL) and one recorder
input was maintained for backward compatibility of scripts.

![Remaining modeling terms in OpenSees source code](/assets/images/ModelingTerms.png)
