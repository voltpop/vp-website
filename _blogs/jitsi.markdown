---
layout: single
permalink: /blogs/jitsi/
collection: blogs
title: Jitsi
date: 2021-07-24
excerpt_separator: <!--excerpt-->

---
## Make un-frustrating what is frustrating.
<!--excerpt-->
Jitsi is awesome. Point. Blank. Period.

Jitsi is not, however, easy for the layman (or even experienced admin) to set up or understand.
with its ultramodular component structure and jvb pooling, it's like... what? I've lately started 
to really come around to the idea of docker-composed apps. I like that they're fairly portable, easily 
configurable, and just plain easy. The learning curve is a bit steep but the future is now and this seems 
to be what all the cool kids are doing. Systems Administration in the very near future is going to be... 
the topic of another article!

anyways, back to deploying jitsi:

### The Server:

I'm using an AWS instance that I've got other docker applications running on. For the purposes of jitsi I'll note 
that this is a t2.medium instance running Ubuntu 20.04 and that I've opened ports 80, 443, 10000, and 4443 in
the security group. Port 443 will catch all of the virtualhosts, but 8080 needs to be open so that the docker network
can bind to it, and redirect it's own traffics, port 10000 if for webRTC (Real Time Communication) magicks...

### Configuring Apache for jitsi:

This one is a bread and butter style redirect.

<div class="term"><VirtualHost *:80>
    ServerName jitsi.domain.name
    ProxyPreserveHost On

    # setup the proxy
    <Proxy *>
        Order allow,deny
        Allow from all
    </Proxy>
    ProxyPass / https://localhost:8443/
    ProxyPassReverse / https://localhost:8443/
</VirtualHost>

<VirtualHost *:443>
    ServerName jitsi.domain.name
    SSLEngine on
    SSLProxyEngine On
    SSLProxyVerify None
    SSLProxyCheckPeerCN Off
    SSLProxyCheckPeerName Off
    ProxyPreserveHost On
    ProxyPass / https://localhost:8443/
    ProxyPassReverse / https://localhost:8443/
    SSLCertificateFile      /etc/letsencrypt/live/servername.domain.name/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/servername.voltpop.com/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf
</VirtualHost>
</div>

### Docker App Deployment In A Nutshell:

1) Grab source

<div class="term">wget https://github.com/jitsi/docker-jitsi-meet/archive/refs/tags/stable-5963.tar.gz</div>

2) Unpack in /opt

<div class="term">~$ cd /opt
~$ tar xvf /root/stable-5963.tar.gz
~$ ln -s docker-jitsi-meet-stable-5963 jitsi
~$ cd jitsi
</div>

3) Configure docker-compose environment

<div class="term">~$ cp env.example .env
~$ vim .env // configure settings appropriately
~$ ./gen-passwords.sh
</div>

4) Configure the `PUBLIC_URL` and `DOCKER_HOST_ADDRESS` in the .env file.

5) Set up local filesystem and deploy the app

<div class=term>~$ mkdir -p ~/.jitsi-meet-cfg/{web/letsencrypt,transcripts,prosody/config,prosody/prosody-plugins-custom,jicofo,jvb,jigasi,jibri}
~$ docker-compose up 
</div>

### Certbot Magic:

Like I said before: I use this server with other docker apps. but I also like to use SSL so how does certbot handle updating
the certificate with an additional domain name? like a boss, that's how.

<div class="term">certbot certonly -d first.domain.com -d second.domain.com -d additional.domain.com</div>

and then select `e` to expand the current certificate.
