mindvessel-wp
=============

This is/will be a recipe for building up a Wordpress installation for
Mindvessel.com.

The recipe assumes a bare Ubuntu 14.04 LTS ("Trusty") server as a starting
point.

Server Inventory
----------------------------------------
The following server groups must exist:

* wordpress-servers — the Apache/PHP servers for Wordpress
* database-servers — the database servers that Wordpress will talk to

The following host vars must be set. Vagrantfile sets them for testing, your
Ansible inventory file will need to set them for real runs.

* domain — For wordpress-servers, the domain for the Apache vhost
* fastcgi_listen — For wordpress-servers, the FastCGI IP:Port where PHP listens
* website_root — For wordpress-servers, where Apache serves from
* wordpress_db_pass — password to set for the wordpress database user account

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

Database
----------------------------------------
Sigh. Provisioning databases with Ansible is radically different depending
whether you have manually controlled server install vs. an AWS RDS instance.
This makes testing with Virtualbox very tricky. For now I'm just doing a bare
minimum Virtualbox setup. Later I will swing back through and add the RDS code.
