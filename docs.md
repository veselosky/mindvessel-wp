Developer Documentation
=========================================
This document should give you some background in case you want to make changes
to these recipes.

Overall Design
----------------------------------------
Our strategy is to create a user (webmaster) whose job is to own web content,
and place all virtual host document roots under the webmaster's home directory.
This makes the home partition the single point of backup, and since it will be
on an EBS volume, it can easily be backed up using snapshots. This also makes it
relatively easy to lock down permissions. Files will be owned by webmaster,
while daemons will run as www-data.

The system is designed to accommodate a single Wordpress install with multi-site
enabled, and as many static site domains as you want.

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

Wordpress sites will be all-in, with Wordpress owning the root, rather than a
hybrid with Wordpress residing in a sub-directory.

Installing Wordpress in a secure and cleanly upgradeable way is not remotely simple.

The files inside the Wordpress archive are always in a top-level "wordpress"
directory, with no version specifier. This means if you just unarchive it in
place, it will clobber the existing install, which is not a smooth way to
upgrade. To correct this, my recipe renames the wordpress directory to
something version-specific after unarchiving, and uses a symlink to point the
server at the correct version.

Keeping content separate from code is very difficult. Using define statements in
``wp-config.php`` you can relocate the wp-content directory outside the code
root. However, Wordpress is hard-coded to look for the themes directory under
the content directory, otherwise it cannot find its default themes. I create a
symbolic link under the content directory to point at the default themes. Then I
register an additional themes directory to keep external themes separate from
the Wordpress code (untested).

Likewise, using define statements I have relocated the plugins directory outside
the Wordpress code directory, so they can be maintained separately.
