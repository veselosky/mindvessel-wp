Server Automation
=============================================

This repository contains the automation scripts for building out Vince's infrastructure. Which at the moment is not much. :)

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

* webservers — Apache servers for statics or applications
* database-servers — database servers if your apps need them

Variables
----------------------------------------
The following host vars must be set. Vagrantfile sets them for testing, your
Ansible inventory file will need to set them for real runs.

* webmaster_pass — Password for the webmaster user
* webmaster_crypt_pass — Crypted version for UNIX account

Other optional variables, and their defaults:

    webmaster: webmaster
    webmaster_home: /home/{{webmaster}}
    websites_home: "{{webmaster_home}}/websites"
    defaultsite_root: "{{websites_home}}/default"
    defaultsite_domain: localhost
    http_service: apache2
    http_service_user: www-data
    http_service_group: www-data
