---
title: Discarded Wrappers
category: Constitutive Models
---

In writing a [previous post]({% post_url 2020-07-11-theres-a-wrapper-for-that %}) on material wrappers available in OpenSees, I knew there was a wrapper that never made
it into cvs, svn, or GitHub. I thought it was no big deal and decided to let it go. Then, looking back at some previous
work on SSI, I re-discovered a second wrapper that never made its way to source
control either.

So, after some intense navel gazing, I decided that two discarded wrappers was enough waste to be recycled in to a new post.

<p><img style="display:block;" src="https://live.staticflickr.com/3090/3100509147_215ee17eaa.jpg" alt="send a love letter this week"><a href="https://www.flickr.com/photos/97719890@N00/3100509147">"send a love letter this week"</a><span> by <a href="https://www.flickr.com/photos/97719890@N00">stevendamron</a></span> is licensed under <a href="https://creativecommons.org/licenses/by/2.0/?ref=ccsearch&amp;atype=html" style="margin-right:5px;">CC BY 2.0</a><a href="https://creativecommons.org/licenses/by/2.0/?ref=ccsearch&amp;atype=html" target="_blank" rel="noopener noreferrer" style="display:inline-block;white-space:none;margin-top:2px;margin-left:3px;height:22px !important;"><img style="height:inherit;margin-right:3px;display:inline-block;" src="https://search.creativecommons.org/static/img/cc_icon.svg"><img style="height:inherit;margin-right:3px;display:inline-block;" src="https://search.creativecommons.org/static/img/cc-by_icon.svg"></a></p>

The first discarded wrapper is _PenaltyMaterial_, which adds a small stiffness to its wrapped _UniaxialMaterial_ object.
This wrapper can help you avoid a singular stiffness due to perfect plasticity and is a downsized approach to putting the
wrapped material in parallel with an _ElasticMaterial_.

I could not find the source code for _PenaltyMaterial_ anywhere, even on this
[Zip disk]({% post_url 2019-11-01-opensees-source-control-from-zip-to-git %}). I did, however, find
Tcl input files where I used the wrapper.

![Screenshot of penalty material](/assets/images/ScreenShotPenalty.png)

To prove I'm not making this up, the class tag for _PenaltyMaterial_ appears on line 127 in the first version of
`SRC/classTags.h` archived on OpenSees GitHub. Furthermore, its object file appears in the first version of
`SRC/material/uniaxial/Makefile` on line 54.

No source code, no problem--re-coding the _PenaltyMaterial_ was easy. Below is an example with the penalty stiffness
equal to 5% of the initial stiffness; although when using this wrapper you'll want to specify a much smaller value.

![Penalty material](/assets/images/WrapperPenalty.png)

The second discarded wrapper is _MultiplierMaterial_, which multiplies the stress and tangent of the wrapped _UniaxialMaterial_
object by a factor. This wrapper can be used to apply overstrength factors to materials and _p_-_y_ multipliers for shadowing
effects in pile groups. An example is shown below with a multiplier of 0.8.

![Multiplier material](/assets/images/WrapperMultiplier.png)

The class tag for _MultiplierMaterial_ first appeared on line 130 of `classTags.h` with
[this commit](https://github.com/OpenSees/OpenSees/commit/d0fc338baacaf04574d81f12ffb258846fbde649#diff-89cc89c0954fc68726a0e9ece192a82b) in January 2018.
Fortunately, I located the source code for this wrapper.

I put both wrappers in GitHub for safekeeping with [PR #407](https://github.com/OpenSees/OpenSees/pull/407). If either of these wrappers sounds useful to you,
please let me know and I'll get the input commands checked in. I would do it now, but my fork of OpenSees is kinda in
disarray at the moment.