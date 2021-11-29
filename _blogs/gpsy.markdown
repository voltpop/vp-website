---
layout: single
permalink: /blogs/gpsy/
collection: blogs
title: Gnu screen Profile SYstem
date: 2021-11-29
excerpt_separator: <!--excerpt-->

---
## The screen wrapper that no one asked for is finally here!
<!--excerpt-->

For those of you not in the know: `screen` is a "full-screen terminal multiplexer". If you can understand that you probably already know what screen is, In regular people terms. screen is a special process that can split and share a terminal between several other processes (like interactive shells and whatnot). You may ask yourself: "self, why is this useful?".

There are a million and a half uses for screen from managing long running processes on remote servers to adding special functionality to your terminal. If you, like me, spend a lot of time working in a terminal, then you may benefit from `gpsy`!

## Customized sessions

While I have a lot of professional friends that prefer using tmux wrappers for their personal use, I'm a longtime screen fanboy. I've got a [.screenrc](https://www.gnu.org/software/screen/manual/screen.html#toc-Customizing-Screen) (from the .rc `runcommand` nomenclature) about a mile long, loaded with goodies as well as several bespoke profiles for other tasks like sftp and IRC.

So I did what any \*nix person would do: I wrote a wrapper script for it!

Check it out here: [Gpsy](https://github.com/dfoulks1/gpsy)

Over time that script grew and grew into what it is today which to be fair, isn't much. but I've managed to use it everyday for the last decade and it's been 100% everytime. the files are all set out as needed in usr/ and can be dropped into place quickly and easily. Included are also some sample profile files that can be added or updated.

If it doesn't work for you, no hard feelings? :)
