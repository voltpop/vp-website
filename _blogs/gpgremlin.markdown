---
layout: single
permalink: /blogs/GPGremlin/
collection: blogs
title: GPGremlin
date: 2021-07-10
excerpt_separator: <!--excerpt-->

---
## GPGremlin: A handy little helper for your GPG woes!
<!--excerpt-->

[Download Gremlin](https://github.com/warwalrux/GPGremlin/archive/refs/heads/main.zip)

A gremlin is a mischievous folkloric creature that causes malfunctions in aircraft. While not good for airplanes, They're good at keeping keys.

I recently ran into an issue where I needed to synchronize keys from several keyservers into a single keyring for file encryption (thanks hiera!) 
and while I know how to do this with gpg I thought it might be fun to create a tool that could streamline the process so that when it comes time
to rebuild / extend the keyring you're not stuck in the laborious process of having to bust out your best gpg-fu looking for keys all over the web,
one by one.

I know right know it seems kind of specific in it's use case but I think with some development, this could be a pretty neat-o little tool.

### Setting Up GPGremlin

  * wget https://github.com/warwalrux/GPGremlin/archive/refs/heads/main.zip
  * cd into GPGremlin
  * pip install -r requirements.txt
  * ln -s $pwd/gremlin ~/bin/gremlin

Open config.yml and set the following:
    - keyservers            -- a list of keyserver urls to check
    - keyring_config_path   -- $pwd/keyrings
    - gpg_home              -- optional, if unset defaults to: ~/.gnupg/
    - min_key               -- minimum keysize
    - table_format          -- format of tables upon printing

### Usage

#### Configuring a keyring config:

In the keyrings directory: create a .json file named for your keyring. 

a keyring object is a yaml dictionary of key / value pairs where the key
is the search criteria (typically an email address) and the value is the
hash of the key for which you are looking.

```
---
keys:
  "dfoulks@apache.org": '6547814F1305619989803CA8C70B04130E01135B'
```

#### Creating and Exporting a keyring for distribution

If you have a use case and a configured keyring file, with two commands you can
create a keyfile containing several keys

```
# Create the named keyring
$ gremlin -cn <your-keyring>

# Armored export of the named keyring
$ gremlin -en <your-keyring>
```

If it doesn't work for you: no hard feelings? :)
