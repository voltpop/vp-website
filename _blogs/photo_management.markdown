---
layout: single
permalink: /blogs/photo-mgmt/
collection: blogs
title: Managing Virtual Photo collections on Linux
date: 2022-06-16
excerpt_separator: <!--excerpt-->

---

## like _thousands_ of pictures...
<!--excerpt-->

I was working with my NextCloud instance the other day trying to manage the backed up photo rolls from our photo rolls, when it struck me that I hadn't seen a good Picture management how-to, so I figured I'd take a crack at writing one for everyone who is just tired of googling what to do with those large collections of pictures (or even worse: manually clicking through each one and organizing it)...

I've decided that I'll cover two of the most common picture formats: JPEG (android) and HEIC (iPhone).

[TL;DR](#wrapping-it-up)

### JPEGs
Joint Photographic Experts Group format is a standard picture compression format. What's most important is that this format contains juicy details in the metadata that can be used to discern things about the picture itself. using `exif` the exif data viewer for Ubuntu, we can extract this data and use it to take an initial sweet of organizing our large albums.

<div class="term">
~# apt install exif
~# for jpg in `ls | grep -i .jpg`; do newname=$(exif $jpg | grep "Date and Time (Origi" | awk -F'|' '{print $NF}' | sed -e 's/ /\_/g' -e 's/:/-/g'); mv $jpg $newname; done 
</div>

### HEIC Files
High Efficience Image Format (HEIC) files are apple proprietary image files which typically contain an image or sequence of images. In order to work with these files they must be converted to jpg using the utilities provided by `heif-gdk-pixbuf`. 

<div class="term">
~# apt install heif-gdk-pixbuf

\# Bulk Conversion
~# for file in \*.HEIC; do heif-convert $file ${file/%.HEIC/.JPG}; done
</div>

Once installed the `heif-convert` utility can be used to convert the files into the JPEG format which can then be analyzed using the same `exif` tool found above. 

## Wrapping it up
Once renamed these pictures can be swept over and parsed to organize them into a sane directory structure. I personally like `~/Pictures/yyyy/mm/` or even `~/Pictures/yyyy/mm/dd/` organization hierarchy,
this is totally optioanl though.

To Recap:
  * HEIC files can be converted into JPG files with metadata in-tact
  * JPG files can be inspected using the exif utility, and subsequently renamed to the date they were taken.
  * Once renamed and in the correct format they can be organized by date (and most importantly in a portable easily readable format)
 
For an all inclusive script that can convert, rename and organize see [here](https://raw.githubusercontent.com/warwalrux/tools/main/pic-sort).

