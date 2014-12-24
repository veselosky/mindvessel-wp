mindvessel-wp
=============

This is/will be a recipe for building up a Wordpress installation for
Mindvessel.com.

The recipe assumes a bare Ubuntu 14.04 LTS ("Trusty") server as a starting
point.

Following host vars must be set. Vagrantfile sets them for testing, your
Ansible inventory file will need to set them for real runs.

* domain
* fastcgi_listen
* website_root

Getting Apache/PHP Working
----------------------------------------
As of Apache 2.4 the default MPM is now mpm_event, which is great. We set up PHP
using FastCGI and the php5-fpm process manager. The out-of-the-box settings seem
reasonable for a small-scale server. Scaling up will require setting several
additional parameters in the recipe to tune performance to the host. We'll cross
that bridge when we get there. Using FastCGI means the Apache processes will not
be bloated with PHP cruft, so static files should serve very efficiently. Using
a static file cache for Wordpress should mean a small server can scale pretty
well.

Ubuntu 14.04 "Trusty" ships with Apache 2.4.7. It includes mod_proxy_fcgi, but
unix domain sockets are not supported until 2.4.8. Nevertheless, Trusty comes
configured for php5-fpm to listen on a unix domain socket, not a TCP socket.
Thus, we need to reconfigure php5-fpm to listen on a TCP socket.

Ref: https://bugs.launchpad.net/ubuntu/+source/apache2/+bug/1324828
