Apache+PHP build pack (Drupal Edition)
========================

This is a build pack bundling PHP and Apache for Heroku apps.

It has been customized to install Drush and the intention is to build a Drupal site from an install profile following the [Drupal.org distro package conventions](http://drupal.org/node/1476014). I have a [blog post that's a work in progress](https://github.com/patcon/patcon.github.com/blob/master/_posts/2012-11-16-deploying-drupal-install-profiles-to-heroku.md), if you're looking for a bit more context.

Heroku supports many add-ons, all of which can be activated from the command-line. This makes it totally simple to take advantage of killer new tools without investing in infrastructure. Some of the options include:

- [MySQL](https://addons.heroku.com/cleardb)
- [Memcache](https://addons.heroku.com/memcachier)
- [Solr](https://addons.heroku.com/websolr)
- [NewRelic](https://addons.heroku.com/newrelic) (monitoring)
- [Blitz](https://addons.heroku.com/blitz) (load-testing)
- [Tons more!](https://addons.heroku.com/)

Configuration
-------------


The config files are bundled with the build pack itself:

* conf/httpd.conf
* conf/php.ini


Pre-compiling binaries
----------------------

    # apache
    mkdir /app
    wget http://apache.cyberuse.com//httpd/httpd-2.2.19.tar.gz
    tar xvzf httpd-2.2.19.tar.gz
    cd httpd-2.2.19
    ./configure --prefix=/app/apache --enable-rewrite
    make
    make install
    cd ..

    # php
    wget http://us2.php.net/get/php-5.3.6.tar.gz/from/us.php.net/mirror
    mv mirror php.tar.gz
    tar xzvf php.tar.gz
    cd php-5.3.6/
    ./configure --prefix=/app/php --with-apxs2=/app/apache/bin/apxs --with-mysql --with-pdo-mysql --with-pgsql --with-pdo-pgsql --with-iconv --with-gd --with-curl=/usr/lib --with-config-file-path=/app/php --enable-soap=shared --with-openssl
    make
    make install
    cd ..

    # php extensions
    mkdir /app/php/ext
    cp /usr/lib/libmysqlclient.so.15 /app/php/ext/

    # pear
    apt-get install php5-dev php-pear
    pear config-set php_dir /app/php
    pecl install apc
    mkdir /app/php/include/php/ext/apc
    cp /usr/lib/php5/20060613/apc.so /app/php/ext/
    cp /usr/include/php5/ext/apc/apc_serializer.h /app/php/include/php/ext/apc/


    # package
    cd /app
    echo '2.2.19' > apache/VERSION
    tar -zcvf apache.tar.gz apache
    echo '5.3.6' > php/VERSION
    tar -zcvf php.tar.gz php


Hacking
-------

To change this buildpack, fork it on Github. Push up changes to your fork, then create a test app with --buildpack <your-github-url> and push to it.


Meta
----

Created by Pedro Belo.
Many thanks to Keith Rarick for the help with assorted Unix topics :)
