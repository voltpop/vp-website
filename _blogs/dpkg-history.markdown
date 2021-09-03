---
layout: single
permalink: /blogs/dpkg-history/
collection: blogs
title: dpkg-history
date: 2021-07-03

---

## Be the change you wish to see in the world

As a (primarily) Ubuntu user, one thing I can safely say about my time working with RHEL is this: I miss `yum history`...

so I decided to get started writing this little ditty:

<center><big><a href="https://github.com/warwalrux/dpkg-history/releases">dpkg-history!</a></big></center>

dpkg-history parses the dpkg.log file to give you _some_ yum history like functionality, with more on the way!

dpkg-history v0.1 beta "Just Stable Enough" edition is (in its current incarnation) a diagnostic tool that allows debian system administrators to get a quickly generated at-a-glance of the currently logged dpkg history without having to dig through dpkg.log files.


### Set Up

  * Download the latest release above or clone the [git repo](https://github.com/warwalrux/dpkg-history)
  * run `pip3 install -r dpkg-history/requirements.txt`
  * dpkg-history can be linked or deployed and updated with git pull.

### Usage

I'm still working on some things but for right now:

What works:
  * `--list`          Show last 30 days worth of jobs in dpkg.log
  * `--show-last`     Show last <num> days of jobs
  * `--show-all`      Show **ALL** jobs in dpkg.log
  * `--info`          Show detailed information about the specified job
  * `--inspect`       Dump the jobs raw entries from the logfile

### Thoughts about dpkg-history

I'm still working on this but that doesn't mean that I'm not always looking for new feature ideas and new ways of doing things.
I like to consider myself a professional learner (as in I learn things professionaly, otherwise would just be silly...) and so
if you have an idea, please feel free to drop me a line or even better hit me up on github right on the repo itself.

If it doesn't work for you, no hard feelings? :) as always: PRs welcome!
