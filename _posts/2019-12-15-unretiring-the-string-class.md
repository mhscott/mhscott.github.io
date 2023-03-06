---
title: Unretiring the String Class
category: Programming
---

If you've compiled the OpenSees source code, you may have noticed the
[`SRC/string`](https://github.com/OpenSees/OpenSees/tree/master/SRC/string)
directory, whose two files, `G3string.h` and `G3string.cpp`,
implement character strings as first-class objects, complete with
overloaded operators and deep copies.

_G3string_ is based on the _String_ class, which I used for programming
projects in my undergraduate computer science courses. In the `G3string.h`
header file you'll see `#define _PLSTRING_H`, which I'm fairly certain is a
reference to Jo Ellen **P**erry and Hal **L**evin, authors of the
[C++ book](https://www.amazon.com/Introduction-Object-Oriented-Design-Ellen-1996-01-03/dp/B01FIXGH1E)
we used in CSC 114 and 210 at Pine State University.

In graduate school, I thought the _String_ class would be a good addition to
G3--I mean, we should return _String_ objects instead of `const char *` and
use `==` instead of `strcmp`, right? But, beyond recorders and parameters,
core string functionality is only marginally useful for finite element
implementation. This functionality becomes even less useful considering all
the string libraries available in Tcl, and now Python.

I was in charge of the Visual Studio workspace at the time, so despite the
limited utility of string objects in G3, I created a dedicated `string`
project for _G3string_ alongside `element`, `material`, and the two dozen or
so other projects. The `string` project is still in the OpenSees Visual Studio
workspace today.

However, there's never been a Makefile in the `SRC/string` directory, making
_G3string_ a member of the OpenSees retirement home--the source code that
remains in the repository but is either not compiled or compiled but not
linked with the executable or the Python module. There's more residents
than you think.

To address this longstanding oversight, I added a Makefile to the `SRC/string`
directory via [PR #185](https://github.com/OpenSees/OpenSees/pull/185).
The _G3string_ class compiles just fine on Windows and Linux and is now one
step closer to coming out of retirement. For what reason, I don't know.