---
layout: single
permalink: /blogs/ez-tls/
collection: blogs
title: Automatic TLS certificate renewal on Apache 2.4
date: 2021-10-05
excerpt_separator: <!--excerpt-->

---
## Managing SSL/TLS domains using Apache HTTPD wizardry
<!-- excerpt -->

SSL/TLS is not a new thing. HTTPS has been keeping our datas safe over the internet for over 20 years now, but certificate management is still a massive PITA. Since the beginning, The Apache Webserver has been a supporter and proponent of SSL since 1998's `mod_ssl`. With Apache Webserver 2.4, `mod_md` "Managed Domain Module" lets the overburdened administrator offload certificate management to the streamlined, baked in LetsEncrypt handler.

### LetsEncrypt

LetsEncrypt is definitely one of the coolest streamlined solution I've seen. LE (LetsEncrypt) and the ACME (Automatic Certificate Management Environment) mean that HTTPS without hassle is not only possible but [here](https://letsencrypt.org/how-it-works/). Previously LetsEncrypt required a cron job and some manual setup but no longer is that the case!

## Setting up Mod MD
to use mod_md in your apache instance install and enable the module (Example shown uses Ubuntu 20.04)

`apt install libapache2-mod-md
a2enmod md
a2enmod ssl
systemctl apache2.service restart`

### Global configuration

In `/etc/apache2/conf.d/mod_md.conf` add:

`MDCertificateAgreement accepted
MDomain <your.domain.com>
MDContactEmail <you@emailaddress.com>`


### Site configuration

In your `/etc/apache2/sites-available/<yoursite>.conf` file:

`MDomain mydomain.com

<VirtualHost *:443>
  ServerName mydomain.com
  SSLEngine on
  DocumentRoot ...path-you-serve-here...
  ...
</VirtualHost>`

## A note from the author

I'm not the progenitor of `mod_md`, that would be the fine folks over at [Apache HTTPD](http://httpd.apache.org/). However I do know that [this guy's](https://github.com/icing/mod_md) documentation is _really_ good.
