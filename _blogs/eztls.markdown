---
layout: single
permalink: /blogs/ez-tls/
collection: blogs
title: Using `mod_md` for certificate management
date: 2021-10-05
excerpt_separator: <!--excerpt-->

---
## Managing SSL/TLS domains automagically with Apache HTTPD 2.4 wizardry
<!--excerpt-->

SSL/TLS is not a new thing. HTTPS has been keeping our data safe on the internet for over 20 years, but certificate management can still be a massive PITA. Since the beginning, The Apache Webserver has been a supporter and proponent of SSL with 1998's release of `mod_ssl`. Now, Apache Webserver 2.4's `mod_md` (the Managed Domain module) lets the overburdened administrator offload certificate management to a streamlined, baked in LetsEncrypt handler.

### LetsEncrypt

LetsEncrypt is definitely one of the coolest streamlined solutions I've seen in recent memory. LetsEncrypt and the ACME Protocol (Automatic Certificate Management Environment) mean that HTTPS without hassle is not only possible but [available now](https://letsencrypt.org/how-it-works/). 

Using LetsEncrypt requires installing packages, and some manual setup such as running the initial configuration script and configuring a cron job to continue to ensure that the certificate would renew properly. This presented other issues as well such as sending the reconfigure directive to the Apache process to load the new certificates.

mod MD elminiates the need for the overhead orchestration by handling the certificate renewal and subsequent reload natively. With a few simple lines of configuration mod_md just gets things done.

## Setting up Mod MD

### 1. instance install and enable the module (Example shown uses Ubuntu 20.04)
<div class="term">
~# apt install libapache2-mod-md
~# a2enmod md
~# a2enmod ssl
~# systemctl apache2.service restart
</div>
### 2. Configure mod_md itself

In `/etc/apache2/conf.d/mod_md.conf` add:


<div class="term">
MDCertificateAgreement accepted
MDomain your.domain.com
MDContactEmail you@emailaddress.com
</div>

### 3. Configure your site to use mod_md

In your `/etc/apache2/sites-available/<yoursite>.conf` file:


<div class="term">
MDomain mydomain.com

<VirtualHost *:443>
  ServerName mydomain.com
  SSLEngine on
  DocumentRoot ...path-you-serve-here...
  ...
</VirtualHost>
</div>
## A note from the author

I'm not the progenitor of `mod_md`, that would be the fine folks over at [Apache HTTPD](http://httpd.apache.org/). However I do know that [this guy's](https://github.com/icing/mod_md) documentation is _really_ good.

