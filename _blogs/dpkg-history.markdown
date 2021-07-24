---
layout: single
permalink: /blogs/dpkg-history/
collection: blogs
title: dpkg history
date: 2021-07-03

---

## Be the change you wish to see in the world

As a (primarily) Ubuntu user, one thing I can safely say about my time working with RHEL is this: I miss `yum history`...

so I decided to be write this kludgy little ditty:

<center><big><a href="https://github.com/warwalrux/dpkg-history/archive/refs/heads/main.zip">dpkg-history!</a></big></center>

dpkg-history parses the dpkg.log file to give you _some_ yum history like functionality.


### Set Up

  * Download the zipfile above or clone the [git repo](https://github.com/warwalrux/dpkg-history)
  * run `pip3 install -r dpkg-history/requirements.txt`
  * dpkg-history can be linked or deployed and updated with git pull.

### Usage

I'm still working on some things but for right now:

What works:
    * -s/--show -- show all of the jobs in dpkg.log
    * -i/--info -- show information about the specified job

what still _needs_ work:
    * -u/--undo -- undo specified job
    - -i/--info -- could be a lot better

If it doesn't work for you: no hard feelings? :)
