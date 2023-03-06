---
title: Minimal Working Example
category: Community
---

When people post online or e-mail me about what could be a bug in OpenSees, I'll ask for a 
[minimal working example](https://en.wikipedia.org/wiki/Minimal_working_example) (MWE), 
i.e., a simple script that demonstrates the problem. I don't want to deal with elaborate scripts--yours or mine.

So, what does an MWE look like for OpenSees? Here's a non-exhaustive list of OpenSees MWE characteristics:

+ One script file, either `.tcl` or `.py`, containing the entire model and analysis
+ As few Tcl or Python constructs as possible
+ One element, two at most
+ One ground motion file (if required)
+ As few analysis steps as possible

Like OpenSees itself, [the simpler the better]({% post_url 2021-04-14-opensees-is-simple %}) for an MWE.

Going through the process of creating an MWE is sometimes enough to resolve the issue. Often, a "bug" is actually a scripting or input error. 
I would like to believe that's why I don't always receive an MWE when I ask for one.
