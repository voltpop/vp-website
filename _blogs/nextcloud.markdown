---
layout: single
permalink: /blogs/nextcloud/
collection: blogs
title: Nextcloud

---

### We don't need google where we're going...

I had some downtime this weekend so I decided to set up a NextCloud instance for the fam.

I used to run an OwnCloud instance back in the day, but that was quite a while ago,
and I haven't even tried since. Although If you know anything about me, you probably
know that my wife is an author.

### The Reason:
I've spent several years trying to figure out the _best_ way to solve a very simple
problem:

how do I keep files backed up, centeral, in sync across devices, and safe from prying eyes

the original answer:
	subversion
	
This worked very well for quite some time, however this solution is fairly technical and
relies on the use of specific tools in order to be fully effective (don't use .odt files
more or less).

### The Approach

After I spent about an hour looking up good ways to deploy everything I want to deploy on
this particular server. I settled on a composed docker application stack to serve all of
the nextcloud components necessary.

It didn't take long to set up and required very little actual configuration.

#### Deployment and Prerequisites

I Hate Waiting: first thing I do is submit all of the required DNS records:
  * office.domain.com
  * nextcloud.domain.com

That way by the time everything else is up and ready DNS has propagated and I can SSH right
in with the friendly DNS name instead of the AWS butchery, because that's how I roll.

Then I deployed an Ubuntu 20.04 AWS instance (t2.medium in this case) and did the following:

a) installed some pre-requisite packages:

  * apt-transport-https
  * ca-certificates
  * curl
  * gnupg-agent
  * software-properties-common

b) Added the docker repository and installed docker-ce and docker-compose

*Code*:
```
apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
apt -y install docker-ce
usermod -aG docker ${USER}
curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

#### Install and Configure apache2

```
apt install apache2
a2enmod ssl proxy proxy_http proxy_wstunnel rewrite headers
systemctl restart apache2
echo "Protocols h2 http/1.1" >> /etc/apache2/apache2.conf
```

##### Create /etc/apache2/sites-available/010.nextcloud.conf

```
<VirtualHost *:80>
  ServerName nextcloud.domain.com
  ErrorLog ${APACHE_LOG_DIR}/nextcloud-error.log
  CustomLog ${APACHE_LOG_DIR}/nextcloud-access.log combined
  ProxyPreserveHost On
  ProxyPass / http://127.0.0.1:8080/
  ProxyPassReverse / http://127.0.0.1:8080/
  RewriteEngine On
  RewriteRule ^/\.well-known/carddav http://%{SERVER_NAME}/remote.php/dav/ [R=301,L]
  RewriteRule ^/\.well-known/caldav http://%{SERVER_NAME}/remote.php/dav/ [R=301,L]
</VirtualHost>

<VirtualHost *:443>
  ServerName nextcloud.domain.com
  ErrorLog ${APACHE_LOG_DIR}/nextcloud-error.log
  CustomLog ${APACHE_LOG_DIR}/nextcloud-access.log combined
  SSLEngine On
  ProxyPreserveHost On
  ProxyPass    / http://127.0.0.1:8080/
  ProxyPassReverse / http://127.0.0.1:8080/
  # Header always set Strict-Transport-Security "max-age=15552000; includeSubDomains"
  RewriteEngine On
  RewriteRule ^/\.well-known/carddav https://%{SERVER_NAME}/remote.php/dav/ [R=301,L]
  RewriteRule ^/\.well-known/caldav https://%{SERVER_NAME}/remote.php/dav/ [R=301,L]
  SSLCertificateFile      /etc/ssl/certs/ssl-cert-snakeoil.pem
  SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
</VirtualHost>
```

##### Create /etc/apache2/sites-available/011.office.conf
```
echo "<VirtualHost *:80>
  ServerName office.domain.com
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
  AllowEncodedSlashes NoDecode
  SSLProxyEngine On
  SSLProxyVerify None
  SSLProxyCheckPeerCN Off
  SSLProxyCheckPeerName Off
  ProxyPreserveHost On
  ProxyPass           /loleaflet https://127.0.0.1:9980/loleaflet retry=0
  ProxyPassReverse    /loleaflet https://127.0.0.1:9980/loleaflet
  ProxyPass           /hosting/discovery https://127.0.0.1:9980/hosting/discovery retry=0
  ProxyPassReverse    /hosting/discovery https://127.0.0.1:9980/hosting/discovery
  ProxyPassMatch "/lool/(.*)/ws$" wss://127.0.0.1:9980/lool/$1/ws nocanon
  ProxyPass   /lool/adminws wss://127.0.0.1:9980/lool/adminws
  ProxyPass           /lool https://127.0.0.1:9980/lool
  ProxyPassReverse    /lool https://127.0.0.1:9980/lool
  ProxyPass           /hosting/capabilities https://127.0.0.1:9980/hosting/capabilities retry=0
  ProxyPassReverse    /hosting/capabilities https://127.0.0.1:9980/hosting/capabilities
</VirtualHost>

<VirtualHost *:443>
  ServerName office.domain.com
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
  SSLEngine on
  AllowEncodedSlashes NoDecode
  SSLProxyEngine On
  SSLProxyVerify None
  SSLProxyCheckPeerCN Off
  SSLProxyCheckPeerName Off
  ProxyPreserveHost On
  ProxyPass /loleaflet https://127.0.0.1:9980/loleaflet retry=0
  ProxyPassReverse /loleaflet https://127.0.0.1:9980/loleaflet
  ProxyPass /hosting/discovery https://127.0.0.1:9980/hosting/discovery retry=0
  ProxyPassReverse /hosting/discovery https://127.0.0.1:9980/hosting/discovery
  ProxyPassMatch "/lool/(.*)/ws$" wss://127.0.0.1:9980/lool/$1/ws nocanon
  ProxyPass /lool/adminws wss://127.0.0.1:9980/lool/adminws
  ProxyPass /lool https://127.0.0.1:9980/lool
  ProxyPassReverse /lool https://127.0.0.1:9980/lool
  ProxyPass /hosting/capabilities https://127.0.0.1:9980/hosting/capabilities retry=0
  ProxyPassReverse /hosting/capabilities https://127.0.0.1:9980/hosting/capabilities
  SSLCertificateFile      /etc/ssl/certs/ssl-cert-snakeoil.pem
  SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
</VirtualHost>
```

##### Create /etc/apache2/sites-available/999-catchall.conf
```
<VirtualHost *:80>
  RedirectMatch permanent ^/(.*)$ https://www.bing.com
  ErrorLog ${APACHE_LOG_DIR}/catchall-error.log
  CustomLog ${APACHE_LOG_DIR}/catchall-access.log combined
</VirtualHost>

<VirtualHost _default_:443>
  RedirectMatch permanent ^/(.*)$ https://www.bing.com
  ErrorLog ${APACHE_LOG_DIR}/catchall-error.log
  CustomLog ${APACHE_LOG_DIR}/catchall-access.log combined
  SSLEngine on
  SSLCertificateFile   /etc/ssl/certs/ssl-cert-snakeoil.pem
  SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
</VirtualHost>
```

#### Enable the appropriate sites

```
a2dissite 000-default.conf
a2ensite 010-nextcloud.conf
a2ensite 011-collabora.conf
a2ensite 999-catchall.conf

systemctl restart apache2
systemctl status apache2
```

#### LetsEncrypt!

This part is a lot more fun than it used to be. on a 20.04 host you simply need to install
certbot and run it!

```
snap install --classic certbot
certbot --apache
```

and fill out the information.

et Voila!

all of the groundwork has been laid! now we can get into the fun bits...

## Doing the Docker Deed

Well, that was fun... I need a gin drink...

Much better. 

Now that we're here, we can start setting up docker, which fortunately, is nowhere near
as hard as it sounds.

### Setting up the environment

I like putting docker apps in /opt I feel like it's the most appropriate place for them, but that's
like, just my opinion, man...

Anyway, docker looks for a file named `.env` from which it reads all of its envvars for a 
docker-composed app. So, we're going to put some goodies in there so that we can let docker do all 
of the setup while I drink my gin drink.

Here's (basically) what I used:

```
NEXTCLOUD_ROOT=/opt/nextcloud
NEXTCLOUD_IPADDRESS=192.168.1.1
NEXTCLOUD_FQDN=nextcloud.comain.com
COLLABORA_FQDN=office.domain.com
MYSQL_ROOT_PASSWORD=CHANGEME
MYSQL_PASSWORD=CHANGEME
COTURN_SECRET=CHANGEME
```

### The docker-compose.yml file

This part is a little bit more involved so feel free to copy and paste:

```
networks:
 nextcloud:

services:
  nextcloud:
    image: nextcloud
    container_name: nextcloud
    networks:
      - nextcloud
    ports:
      - "127.0.0.1:8080:80"
    volumes:
      - ${NEXTCLOUD_ROOT}/html:/var/www/html
      - ${NEXTCLOUD_ROOT}/data:/srv/nextcloud/data
    extra_hosts:
      - "${NEXTCLOUD_FQDN}:${NEXTCLOUD_IPADDRESS}"
      - "${COLLABORA_FQDN}:${NEXTCLOUD_IPADDRESS}"
    depends_on:
      - mariadb
      - redis
    environment:
      - NEXTCLOUD_TRUSTED_DOMAINS='${NEXTCLOUD_FQDN}'
      - NEXTCLOUD_DATA_DIR=/srv/nextcloud/data
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_HOST=nextcloud-mariadb
      - REDIS_HOST=nextcloud-redis
    restart: unless-stopped

  mariadb:
    image: mariadb
    container_name: nextcloud-mariadb
    command: --innodb_read_only_compressed=OFF
    restart: unless-stopped
    volumes:
      - ${NEXTCLOUD_ROOT}/mariadb:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
    networks:
      - nextcloud

  redis:
    image: redis
    container_name: nextcloud-redis
    networks:
      - nextcloud
    restart: unless-stopped

  coturn:
    image: instrumentisto/coturn
    container_name: nextcloud-coturn
    restart: unless-stopped
    ports:
      - "3478:3478/tcp"
      - "3478:3478/udp"
    networks:
      - nextcloud
    command:
      - -n
      - --log-file=stdout
      - --min-port=49160
      - --max-port=49200
      - --realm=${NEXTCLOUD_FQDN}
      - --use-auth-secret
      - --static-auth-secret=${COTURN_SECRET}

  collabora:
    image: collabora/code
    container_name: nextcloud-collabora
    restart: unless-stopped
    networks:
      - nextcloud
    ports:
      - "127.0.0.1:9980:9980"
    extra_hosts:
      - "${NEXTCLOUD_FQDN}:${NEXTCLOUD_IPADDRESS}"
      - "${COLLABORA_FQDN}:${NEXTCLOUD_IPADDRESS}"
    environment:
      - 'domain=${NEXTCLOUD_FQDN}'
      - 'dictionaries=en'
    cap_add:
      - MKNOD
    tty: true
```

This sets up 5 different docker containers, each running one service with its
configuration and runing directives nested within each configuration object.

these containers run on a localized docker container network named `nextcloud`.

we can eventually run multiple docker networks on a given host. I'll prpobably stub
my toe on that later when I set up jitsi using the _exact same method_.

From here we should be about ready to start up the docker stack.

Also Please Note:

the `command` directive on the mariadb service container is absolutely essential, if
you forget it you'll whang your noggin on this:

```General Error: 4047 InnoDB refuses to write tables with ROW_FORMAT=COMPRESSED or KEY_BLOCK_SIZE```

### A Note about using docker-compose

When running docker-composed apps it's important to have a solid grasp of the basics

*pull*
```docker-compose pull```

*dump old images*
```docker image prune --force```

*check running containers*
```docker ps```

*start composed app*
from the directory in which `docker-compose.yml` *_AND_* `.env` can be found
```docker-compose up -d```

*stop composed add*
from the directory in which `docker-compose.yml` *_AND_* `.env` can be found
```docker-compose down```

### Some Thoughts as we wrap up

Congratulations on getting this far! 
This was my third try at setting up NextCloud. The first time I tried installing it using
the snap only to realize that in order to set up collabora I'd have to deploy a docker instance
anyway because that's just how collabora works. 

My second try was using the php zipfile in /var/www and using apache to broker everything. In the end
though this was the best way to set things up and secure them (running in AWS as I am) I don't have to
add all of the ports to my security profile which greatly streamlines the setup of this instance.

#### Back to work

Moving right along: navigate to https://nextcloud.domain.com/nextcloud in a browser and you should be
presented with a prompt to "create an admin user"

once you've entered a username and password you'll be prompted to configure the database connection.

for this you should select `mariadb` and enter the password mysql_password configured in the .env file.

once you've done that you should simply be able to log in with the user you _just_ created.

#### F!@#$@& COLLABORA

All this talk about collabora and it still doesn't work?! Don't fret, the UI _is_ cool af and if you're
anything like me, the shiny caught your attention and you tried to go play with all the pretty features.
Take heed traveler: before you go editing .odt files in the browser you have to enable the collabora plugin,
otherwise you're going to stub your toe on the office suite.

Under the you icon in the top right corner you should see apps

# screenshot here

Under office tools in the menu on the left, you should find the collabora plugin tile. Select it to enable it


now you're done, go you!
