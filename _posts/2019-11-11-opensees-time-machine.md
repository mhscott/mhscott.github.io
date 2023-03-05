---
title: OpenSees Time Machine
category: Community
---

I recently found two [Zip disks](https://en.wikipedia.org/wiki/Zip_drive)
in my office. They were the next big thing
for about 15 minutes in the late 1990s and early 2000s.

I then got my hands on an external Zip drive and was able to offload the
disks' contents. One disk has lecture notes and assignments from a few
graduate courses I took. Those materials will come in handy the next time I
teach finite elements or computational mechanics.

The other disk has the OpenSees source code from 2001 and a version 1.2
Windows executable.

Putting items on a Zip disk was one of many common pre-cloud behaviors that
are now considered odd.  So, examining the disks is like studying the
artifacts of an ancient culture. What follows is my archaeological report.

---
<br>
I double-clicked the version 1.2 Windows executable, not expecting it to run,
just hoping that it wouldn't corrupt my computer. Nothing terrible happened,
only a system error regarding Tcl 8.3.

![OpenSees version 1.2.0](/assets/images/OpenSees1.2.png)

Today, OpenSees is on version 3.1.0 and it requires Tcl 8.6, which [this
Python3 module](https://pypi.org/project/openseespy/) is going to swallow
whole. 

The source code snapshot is far more intriguing. When OpenSees moved to
GitHub, the SVN logs were brought along, but only back to 2011. I'm not sure
why the logs stopped there, but it means the 2001 source code pre-dates
written history.

I could go through all the folders and catalog the differences between then
and now, but that would be incredibly boring. Instead, I'll focus on the two
directories, `SRC/element` and `SRC/material/uniaxial`, where most contributions
from the OpenSees community have landed over the years.

Here are the subdirectories in the `SRC/element` directory of 2001.

![OpenSees element subdirectories in 2001](/assets/images/OpenSeesElements2001.png)

There was, more or less, one element class in each subdirectory.

Here are the subdirectories in the `SRC/element` directory today.

![OpenSees element subdirectories in 2019](/assets/images/OpenSeesElements2019.png)

Many of these subdirectories, e.g., `UWelements` and `PFEMelement`,
contain multiple element classes. Plus, some of the subdirectories from
2001 have new additions, e.g., the thermal formulations in `dispBeamColumn`.

Turning to the `SRC/material/uniaxial` directory, here are its
subdirectories and cpp files from 2001.

![OpenSees uniaxial material subdirectories in 2001](/assets/images/OpenSeesMaterials2001.png)

Woah! There's only _Steel01_ and _Concrete01_, and wrappers around
FORTRAN code for Drain and Fedeas material models. Did the users of
OpenSees version 1.2 belong to a primitive hunter-gatherer society? How did
they complete Ph.D.s and earn tenure with such basic tools?

Compare that dearth of models to what's in the `SRC/material/uniaxial`
directory today.

![OpenSees uniaxial material subdirectories in 2019](/assets/images/OpenSeesMaterials2019.png)

Note that I had to change my terminal window from 80x24 to 155x28 to make
everything fit for the screenshot. There are now several concrete and steel
and IMK models, and lots of other stuff--a grocery store on every corner.
But you still need to hunt and gather to earn tenure.

---
<br>
If GitHub suddenly disappeared, somehow taking everyone's local repos away
with it, we could reconstruct OpenSees from this Zip disk. If that did
happen, what should we do differently? Is there a
[Lougle](https://en.wikipedia.org/wiki/Hot_Tub_Time_Machine)
opportunity here? Let me know your thoughts in the comments section.