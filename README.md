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

Variables
----------------------------------------
The following host vars must be set. Vagrantfile sets them for testing, your
Ansible inventory file will need to set them for real runs.

* domain — For wordpress-servers, the domain for the Apache vhost
* fastcgi_listen — For wordpress-servers, the FastCGI IP:Port where PHP listens
* webmaster_pass — Password for the webmaster user
* webmaster_crypt_pass — Crypted version for UNIX account
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

Ubuntu's default Apache configuration is pretty solid, defaulting to "Deny all"
for the entire file system. Individual directories must explicitly grant access
in order to serve web content.

Our strategy is to create a user (webmaster) whose job is to own web content,
and place all virtual host document roots under the webmaster's home directory.
This makes the home partition the single point of backup, and since it will be
on an EBS volume, it can easily be backed up using snapshots. This also makes it
relatively easy to lock down permissions. Files will be owned by webmaster,
while daemons will run as www-data.

The system is designed to accommodate a single Wordpress install with multi-site
enabled, and as many static site domains as you want.

TODO: Lock down the ``open_basedir`` setting in ``php.ini``.
Ref: http://wordpress.stackexchange.com/questions/58391/

Database
----------------------------------------
Sigh. Provisioning databases with Ansible is radically different depending
whether you have manually controlled server install vs. an AWS RDS instance.
This makes testing with Virtualbox very tricky. For now I'm just doing a bare
minimum Virtualbox setup. Later I will swing back through and add the RDS code.

The Wordpress recipe assumes the existence of a MySQL server previously set up
to use the .my.cnf authentication method. The Wordpress recipe creates the
Wordpress database and user.

Wordpress
----------------------------------------
I've done my best to follow security best practices in setting up the Wordpress
installation. I cribbed from a few recipes found on Ansible Galaxy and the
ansible-examples Github repo.

Wordpress sites will be all-in, with Wordpress owning the root, rather than a hybrid with Wordpress residing in a sub-directory.
