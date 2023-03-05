---
title: Just Another Python Module
category: Programming
---

When OpenSees got off the ground, many finite element analysis software
packages relied on fixed format input. So, it was important to integrate
OpenSees with a fully functional scripting language to give users flexibility
in creating applications without requiring users to write and compile C++
`main()` functions.

I don't remember all the factors that went in to the selection of a
scripting language for OpenSees. Tcl and MATLAB were the two main choices.
MATLAB wasn't chosen because it's commercial software. Although Octave was
available then, I suspect it wasn't as mature as it is now, so it wasn't
seriously considered.

Python was also considered, but it was a relative newcomer at the time.
Today, it seems pretty silly that Tcl was chosen over Python--hindsight is
20/20. However, I don't consider the selection of Tcl a mistake. It was the
most forward thinking option at the time. "Big data" was just over the
horizon and "data science" was not yet a thing.

The main crux of Tcl is that every variable is a string. While this is good
for navigating directory structures and concatenating file names like Germans
make words, it's not so good for mathematical operations. Tcl requires
`set c [expr $a*$b]` where other languages accept `c = a*b`.

In addition, there are very few packages available for scientific computing
and post-processing with Tcl. I've felt the pain of plotting OpenSees results
on a Tk canvas using emu_graph, which is now defunct. I've also used the
[math::linearalgebra](http://tmml.sourceforge.net/doc/tcllib/linalg.html)
package, which makes its way through numerical linear
algebra using Tcl lists in surprisingly fine fashion.

But I used those Tcl packages only when absolutely necessary. Normal levels
of necessity, e.g., automating the generation of nice looking figures, bred
innovation for those who wrote OpenSees post-processing middleware between
Tcl and MATLAB.

So, with Python's rise in popularity in the mid 2010s and the availability
of numerous libraries, we decided to make OpenSeesPy. No more awkward
middleware.

OpenSeesPy documentation and installation instructions can be found
[here](https://openseespydoc.readthedocs.io/en/latest/).
You'll see that the commands follow the same style as the OpenSees Tcl
commands. This should make your transition pretty smooth. Details of the
software implementation are described in
[Zhu et al (2018)](http://dx.doi.org/10.1016/j.softx.2017.10.009) in case
you ever want to link OpenSees with other languages like R.

As a Python module, OpenSees becomes yet another species in the Python
ecosystem. You start Python, then import `openseespy` right alongside
`numpy` and `matplotlib`, and other modules. This is a lot different from
solipsistic OpenSees, the standalone executable built on top of Tcl.

![OpenSeesPy in terminal window](/assets/images/OpenSeesPy3.6.png)

One of the biggest advantages of using OpenSees with Python is you can embed
your OpenSees analyses in Jupyter Notebooks, either locally or in the cloud.
My colleague in Eastchester has made some good Python tutorial videos. Head
over to her [blog](https://simpsoba.wordpress.com/blog/) to check those out.

---
<br>
I could go on, but instead, here's a webinar that shows the basics of using
OpenSeesPy in a Jupyter Notebook. I hope it convinces you that switching to
OpenSeesPy is a good idea.

[![OpenSeesPy webinar](http://img.youtube.com/vi/lq9mESvpL74/0.jpg)](http://www.youtube.com/watch?v=lq9mESvpL74?t=789 "OpenSeesPy Webinar")
