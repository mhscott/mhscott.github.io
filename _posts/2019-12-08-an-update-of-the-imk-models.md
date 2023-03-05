---
title: An Update of the IMK Models
category: Constitutive Models
---

Hysteretic models include several parameters that can be fit to a variety of
physical phenomena known to occur in steel and reinforced concrete members
under cyclic loading. I did my Ph.D. in the Bay Area, but not at The Farm.
Accordingly, I'm more of a fiber section aficionado--but I get the appeal of
hysteretic models.  Although some physics is lost, hysteretic models scale
much better to large frame models than fiber sections. There's always a
tradeoff.

A particular family of hysteretic models, Ibarra-Medina-Krawinkler (IMK), has
a significant following in the OpenSees community.  The IMK models have been
in OpenSees from very early on, but the latest chapter in the IMK story
arrived with little fanfare via [PR #118](https://github.com/OpenSees/OpenSees/pull/118).
To convey that this pull request should be a big deal requires a brief
background of the IMK models in OpenSees.

The degrading bilinear, pinching, and peak-oriented models that would become
IMK were first developed by Rahmana and Krawinkler for NLDYNA.
The models were also implemented in DRAIN-2DX.  There is a little known
_UniaxialMaterial_ class called _DrainMaterial_ that wraps FORTRAN
subroutines that fit the DRAIN-2DX call signatures.  If you look in the
[`SRC/material/uniaxial/drain`](https://github.com/OpenSees/OpenSees/tree/master/SRC/material/uniaxial/drain) directory now, you'll see only one
proof-of-concept implementation for a bilinear hardening material.
For whatever reason, those DRAIN-2DX hysteretic models never made their way
into OpenSees.

The same degrading bilinear, pinching, and peak-oriented models were also
implemented by [Ibarra and Krawinkler (2005)](https://stacks.stanford.edu/file/druid:dj885ym2486/TR152_Ibarra.pdf)
in a structural analysis code known as SNAP (see Chapter 3 of the report).
In the early 2000s, the SNAP models were recoded in C++ for OpenSees and still reside in the
[`SRC/material/uniaxial/snap`](https://github.com/OpenSees/OpenSees/tree/master/SRC/material/uniaxial/snap)
directory.  You can use these models, collectively
known as the IMK models, via the uniaxialMaterial commands
`Bilinear`, `Pinching`, and `Clough`.

Then in the mid-to-late 2000s came the "ModIMK" models: `Bilin`,
`ModIMKPinching`, and `ModIMKPeakOriented`. I don't know all the particulars
of what the "Mod" part of ModIMK signifies. That's not important. What's
important is that the ModIMK models became widely used.  With all the users,
a few bugs were discovered. No shade intended--you'll find a much higher
density of bugs in the obscure material models that lie fallow in OpenSees.

Some of the ModIMK bugs were fixed, but not all the fixes made their way into the
main OpenSees source code repository. One of my colleagues in Eastchester checked in
some bug fixes, but several other versions of ModIMK with varying levels of functionality
continued to float around. Some of those versions were object-code only. We
even ended up with `Bilin02`, `ModIMKPinching02`, and `ModIMKPeakOriented02`.
I don't know the differences between the originals and the 02 versions. But I do
know all the different versions became a headache to maintain and users
wondered if they had the right version of ModIMK.

Then, earlier this year, [PR #118](https://github.com/OpenSees/OpenSees/pull/118)
added three new _UniaxialMaterial_
classes: `IMKBilin`, `IMKPinching`, and `IMKPeakOriented`.  These
implementations should have the previous bugs resolved. The uniaxialMaterial
command has been modified to accept these models for both the Tcl and Python
interpreters of OpenSees.

The online documentation has not been updated to include these new models, but
here's the input format I gleaned from the source code. If you've used the ModIMK models,
I assume the abbreviated input arguments shown below make sense.

```tcl
uniaxialMaterial IMKBilin tag? Ke? Theta_p_pos? Theta_pc_pos? Theta_u_pos? Mpe_pos? MmaxMpe_pos? ResM_pos? Theta_p_neg? Theta_pc_neg? Theta_u_neg? Mpe_neg? MmaxMpe_neg? ResM_neg? LamdaS? LamdaC? LamdaK? Cs? Cc? Ck? D_pos? D_neg?

uniaxialMaterial IMKPinching tag? Ke? Up_pos? Upc_pos? Uu_pos? Fy_pos? FmaxFy_pos? ResF_pos? Up_neg? Upc_neg? Uu_neg? Fy_neg? FmaxFy_neg? ResF_neg? LamdaS? LamdaC? LamdaA? LamdaK? Cs? Cc? Ca? Ck? D_pos? D_neg? KappaF? KappaD?

uniaxialMaterial IMKPeakOriented tag? Ke? Up_pos? Upc_pos? Uu_pos? Fy_pos? FmaxFy_pos? ResF_pos? Up_neg? Upc_neg? Uu_neg? Fy_neg? FmaxFy_neg? ResF_neg? LamdaS? LamdaC? LamdaA? LamdaK? Cs? Cc? Ca? Ck? D_pos? D_neg?
```

Give these new IMK models a try.  If you find a bug, fix it then submit a
pull request. It's nice to have the official source code for these popular
models back on the main branch of OpenSees.