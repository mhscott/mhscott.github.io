---
title: "OpenSees Source Control: From Zip to Git"
category: Programming
---

Back in the G3 days, when there were essentially two developers, Frank kept the source code up to date on a Zip disk that he stuffed into a 
cargo pocket of his pants each afternoon. Frank instructed me to retrieve the disk if he was ever hit by a bus. As long as the buses and I 
stayed in our lanes, there were no conflicts.

![100 MB ZipDisk](/assets/images/ZipDisk.png)

Frank and the Zip disk survived. In 2000, G3 changed to OpenSees and the source code was put on a server running Concurrent Versions System, 
or [CVS](https://en.wikipedia.org/wiki/Concurrent_Versions_System), a centralized version control system. This made it easy to bring additional 
developers on board from other universities, accelerating the growth of OpenSees.

In 2006, OpenSees source control switched to Apache Subversion, or [SVN](https://en.wikipedia.org/wiki/Apache_Subversion), a more secure system 
than CVS. I never noticed much of a difference between CVS and SVN. Neither did the source code--it remained on the same server.

In September 2018, the OpenSees source code moved to [GitHub](https://github.com/OpenSees/OpenSees), the cloud-based home to millions of software
projects. GitHub is built on Git, a decentralized version control system. Think regional air carriers over major airlines' hub-and-spoke model.

I don't understand much about Git. But if you want to contribute to OpenSees, you should "fork" the project. This will create a copy of the 
repository on your GitHub account and, in a way, you become master of your own domain. From there, you only need to know a few basic 
commands: `pull`, `add`, `commit`, `push`.

The one tricky thing you'll need to learn is keeping your fork up to date with the master branch of OpenSees. I found 
[these instructions](https://gist.github.com/CristinaSolana/1885435) very helpful and have adapted the commands here to be OpenSees-centric.

```bash
# 1. Clone your fork (replace foobar with your GitHub username)
> git clone git@github.com:foobar/OpenSees.git

# 2. Add remote from original repository in your forked repository
> cd into/cloned/OpenSees
> git remote add upstream git@github.com:OpenSees/OpenSees.git
> git fetch upstream

# 3. Update your fork to keep up with changes in OpenSees
> git pull upstream master
```

You only need to do steps 1 and 2 once. After that, make `git pull upstream master` (step 3) part of your weekly routine so your fork never strays 
too far from the main line.

You may run into a conflict when an upstream pull brings in edits to the same lines of code that you have modified in your fork. You will have to 
deal with this head on--compare the versions, inspect the diffs, and make the changes manually. Don't freak out. Don't delete the upstream edits. 
Don't swear never to pull from upstream again.

Finally, please don't e-mail your source code to the OpenSees development team asking us to check it in on your behalf. No matter how politely you 
ask, there are no end arounds like there were in the CVS/SVN era. All updates need to come through pull requests on GitHub. This ensures your code 
is up to date with the latest OpenSees, not the OpenSees from your Great Conflict Freak Out of 2014.
