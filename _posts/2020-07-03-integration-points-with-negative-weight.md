---
title: Integration Points with Negative Weight
category: Element Formulations
---

A colleague in [Eastchester]({% post_url 2020-06-24-where-is-eastchester %}) once told me that faculty have three, maybe 
four, good ideas over their career. In other words, a faculty member 
could have over a hundred papers, but there's only three or four 
underlying concepts. Perhaps it was "two, maybe three", but you get the 
point.

Playing with integration points and weights, i.e., quadrature, in 
force-based frame finite elements is one of my good ideas. I've written 
a handful of papers on the subject, but there's still a few loose ends. 
I don't consider these loose ends to be LPUs because any article that 
tidies up a loose end would be 90% re-hash of previous work. A blog post 
is a much better outlet.

One such loose end is what happens to the computed response of a 
force-based frame element when one of the integration points has a 
negative integration weight. In a couple of those papers and in 
[another post]({% post_url 2019-10-12-behind-the-elastic-interior %}) 
I did my best 
[Mr. Mackey](https://wiki.southpark.cc.com/wiki/Mr._Mackey) 
and wrote "negative integration weights are bad, m'kay", but never showed why.

The reason why is encountering a negative integration weight is very 
unlikely if you don't have any input errors. However, I've learned that 
anything is possible with nonlinear structural analysis in OpenSees, 
thanks to its numerous modeling choices and its free-range approach to 
error checking.

One way your model can have a negative integration weight is due to an 
input error when using plastic hinge integration. For example, your 
input script uses a Tcl or Python expression to compute a plastic hinge 
length, _l<sub>p</sub>_, in terms of concrete compressive strength, 
_f'<sub>c</sub>_, but _f'<sub>c</sub>_ is defined as negative in order to appease the 
_Concrete23_ constructor.

---
<br>
The following example demonstrates the effect of a negative integration 
weight. The model is an _L_=120 inch tall column with bilinear 
moment-curvature response (_EI_=3x10<sup>7</sup> kip-inch<sup>2</sup>,
_M<sub>y</sub>_=4800 kip-inch, $$\alpha$$=0.01) at each integration point.

![Cantilever with plastic hinge and bilinear moment-curvature](/assets/images/ColumnNW.png)

The element integration rule is endpoint plastic hinge integration with 
_l<sub>p</sub>=0.1L_ at the column base. This is not the best plastic hinge 
integration method, but it is a straightforward approach to show what 
happens when a plastic hinge length is negative.

The computed load-displacement response at the top of the column is 
shown below for the "input error" _l<sub>p</sub>=-0.1L_ and the "intended" 
solution using _l<sub>p</sub>=+0.1L_.

![Column load-displacement response](/assets/images/NegativeWeight.png)


---
<br>
There are other modeling approaches, summarized here, that can give 
negative integration weights even with correct inputs, i.e., positive 
plastic hinge lengths and a reasonable number of integration points:

+ Modified Gauss-Radau plastic hinge integration where
_4(l<sub>pI</sub>+l<sub>pJ</sub>) > L_. The length of the elastic interior becomes 
negative, making the weights of the two interior integration points 
negative. This condition is pretty easy to achieve with correct inputs.
+ Any other plastic hinge integration method where _l<sub>pI</sub>+l<sub>pJ</sub> > L_, 
for the same reason as the previous item. It would be difficult to 
satisfy this condition with correct inputs.
+ Quadrature rules with prescribed integration point locations 
can have negative integration weights upon solving the 
Vandermonde equations.
+ Newton-Cotes integration with an odd number of integration points, 
_N_>=9. For example, with _N_=9, integration points 3, 
5, and 7 have negative weight.

These approaches lead to varying degrees of strange results, but are 
generally harmless because the negative integration weights are not at 
the ends of the element. However, there could be unexpected results with 
member loads.

If you see strange results when using force-based frame elements, a 
negative weight could be the culprit, but it's likely something else. 
Regardless, it's a good idea to use the 
[`sectionWeight`](https://openseespydoc.readthedocs.io/en/latest/src/sectionWeight.html) 
command to check the integration weights.
