---
layout: single
permalink: /blogs/GPGremlin/
collection: blogs
title: GPGremlin

---

## GPGremlin: A handy little helper for your GPG woes!

A gremlin is a mischievous folkloric creature that causes malfunctions in aircraft. While not good for airplanes, They're good at keeping keys.

I recently ran into an issue where I needed to synchronize keys from several keyservers into a single keyring for file encryption and while I 
know how to do this with gpg I thought it might be fun to create a tool that could streamline the process.

### Setting Up

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

```
---
keys:
  "dfoulks@apache.org": '6547814F1305619989803CA8C70B04130E01135B'
```

