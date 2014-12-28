mindvessel-wp
=============
This is/will be a recipe for building up a Wordpress installation for
Mindvessel.com.

The recipe assumes a bare Ubuntu 14.04 LTS ("Trusty") server as a starting
point. Currently it is only up to "testing" and not ready for production. Lots
of parameters still need to be tuned.

To run it, you'll need to create a Python virtualenv and install the
dependencies in the ``requirements.txt`` file. Then use standard Ansible
commands to run the desired playbooks. For example:

  ansible-playbook -i my-inventory.ini site.yml

See the docs.md file before making changes.

Server Inventory
----------------------------------------
The following server groups must exist:

* wordpress-servers — the Apache/PHP servers for Wordpress
* database-servers — the database servers that Wordpress will talk to

Variables
----------------------------------------
The following host vars must be set. Vagrantfile sets them for testing, your
Ansible inventory file will need to set them for real runs.

* fastcgi_listen — For wordpress-servers, the FastCGI IP:Port where PHP listens
* webmaster_pass — Password for the webmaster user
* webmaster_crypt_pass — Crypted version for UNIX account
* wordpress_database_pass — password to set for the wordpress database user account
* wordpress_domain — For wordpress-servers, the domain for the Apache vhost
* wordpress_version — What version of Wordpress to install.
* wordpress_sha256 — checksum for the Wordpress archive, to ensure integrity
