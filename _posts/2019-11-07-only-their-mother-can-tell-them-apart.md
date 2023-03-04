---
title: Only Their Mother Can Tell Them Apart
category: Community
---

In January 1999, when OpenSees was known as G3, Prof. Fenves created G2
for teaching nonlinear structural analysis in CE 221 at UC Berkeley. G2
was written in MATLAB with an architecture similar to G3.

I took CE 221 and used G2 for the homework assignments, but after a few
years I lost the code. Oh well, these things happened in the days before
cloud-based storage.

I wrote G2 off as G3's long lost sibling, reminiscent of the 1988 film
[_Twins_](https://en.wikipedia.org/wiki/Twins_%281988_film%29) starring
Arnold Schwarzenegger and Danny DeVito as twins separated
at birth after a genetic experiment yielded unexpected results.

---
<br>
Fast forward to October 2019. One of my good graduate school friends invited
me to give a seminar at his university. After the seminar, we got to talking
about OpenSees, then the conversation turned to CE 221 and G2. I asked my friend
if he kept the G2 source code and he said "Yes, it's right here on my laptop."
After wiping away my amazement at his archiving skills, I offered up a memory
stick and he dragged and dropped.

After I returned home, I took a trip down memory lane. G2 includes linear-elastic,
small displacement truss and beam-column elements and there are a couple of material
nonlinear truss elements. The material nonlinear beam-column elements include the
two-component model and displacement-based and force-based distributed plasticity
formulations. Fiber-discretized cross-sections are implemented for wide-flange shapes
with bilinear stress-strain response. There is also a geometrically nonlinear elastic
truss element.

Newton-Raphson and Modified Newton are the available solution algorithms. These can
be used with a variable step load-control static integrator. It's MATLAB, so the solver
is the `\` operator--no need for options there.

Considering G2 was written for MATLAB v5.0, I was a little surprised it ran so smoothly
on MATLAB v9.5 (R2018b). The only hiccup was adding the G2 directory to the MATLAB path.

To ensure I don't lose the G2 code again, I created a [GitHub repository](https://github.com/mhscott/G2)
after securing the appropriate permissions. You can read more about the element
formulations and solution strategies in the source files. Clone it, fork it, give it a
try--it may be useful for your research. I'll accept pull requests if you would like
to share examples or contribute new functionality.

---
<br>
The title of this post is taken from the tagline of Twins. Here is the scene where
the twins see each other for the first time since birth

[![Twins](http://img.youtube.com/vi/Mw1Z2Jlp9Qw/0.jpg)](http://www.youtube.com/watch?v=Mw1Z2Jlp9Qw "Twins")