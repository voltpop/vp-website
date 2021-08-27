---
layout: single
permalink: /blogs/dpkg-history/
collection: blogs
title: dpkg history
date: 2021-07-03

---

## Be the change you wish to see in the world

As a (primarily) Ubuntu user, one thing I can safely say about my time working with RHEL is this: I miss `yum history`...

so I decided to get started writing this kludgy little ditty:

<center><big><a href="https://github.com/warwalrux/dpkg-history/archive/refs/heads/main.zip">dpkg-history!</a></big></center>

dpkg-history parses the dpkg.log file to give you _some_ yum history like functionality.


### Set Up

  * Download the zipfile above or clone the [git repo](https://github.com/warwalrux/dpkg-history)
  * run `pip3 install -r dpkg-history/requirements.txt`
  * dpkg-history can be linked or deployed and updated with git pull.

### Usage

I'm still working on some things but for right now:

What works:
    * --show -- show all of the jobs in dpkg.log
    * --info -- show detailed information about the specified job
    * --redo -- preform all of the actions in a specified job, again.
what still _needs_ work:
    * --undo -- undo specified job


### Thoughts about dpkg-history

I'm still working on this but that doesn't mean that I'm not always looking for new feature ideas and new ways of doing things.
I like to consider myself a professional learner (as in I learn things professionaly, otherwise would just be silly...) and so
if you have an idea, please feel free to drop me a line or even better hit me up on github right on the repo itself.

If it doesn't work for you: no hard feelings? :)
