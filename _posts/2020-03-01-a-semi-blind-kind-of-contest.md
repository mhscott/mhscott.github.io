---
title: A Semi-Blind Kind of Contest
category: Community
---

Contests where researchers and practitioners blindly predict the response of structural systems have produced some rather 
interesting results. And by "interesting", I mean "all over the place". So much so, that in an effort to protect the 
contestants, the contest organizers rarely make the results publicly available.

Nonlinear structural analysis is hard though. Even with detailed structural drawings, material properties, and loading 
protocols, it's easy to run into problems with units, dimensions, element formulations, constitutive models, numerical
solution algorithms, and boundary conditions.

So, let's take a step back and determine how well we can predict, or rather, agree upon, the linear response of a structural
model that's pretty well defined. A [colleague](https://simpsoba.wordpress.com/) in Eastchester studied the experimental behavior and numerical simulation of 
strongback frame systems during their Ph.D. Braced frames offer a multitude of teaching moments for linear and nonlinear 
structural analysis, so I’ve integrated my colleague's strongback models in my courses. One such model is shown below.

![Strongback frame model and loads](/assets/images/Strongback.png)

The model shown here assumes infinitesimal joints, a simplification of the model with rigid offsets presented in
[Simpson and Mahin (2018)](https://doi.org/10.1061/(ASCE)ST.1943-541X.0001960). 
All members are steel (_E_=200,000 MPa), except the BRB, which we'll assume has an effective _E_ of 300,000 MPa. Note that the
column members on the right side of the frame are turned for weak axis bending. Also note that the left support is fixed 
while the right support is pinned.

So, what is the horizontal displacement (units=mm) of the joint indicated in the figure? The total horizontal load is only 
1 kN, putting the frame response well within the linear-elastic, small displacement range.

E-mail me your answer. You don't have to use OpenSees, 
use whatever software you want or whatever back of an envelope you can find. Let me know if you have any questions.

This will be fun! I look forward to your responses. I will share anonymized results in another post.

---
<br>
Update (March 6, 2020): The shear tabs are intended to be moment releases.

---
<br>
RESULTS
