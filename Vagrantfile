# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define :webserver do |webserver|
    webserver.vm.box = "ubuntu/trusty64"

    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine. In the example below,
    # accessing "localhost:8080" will access port 80 on the guest machine.
    webserver.vm.network "forwarded_port", guest: 80, host: 8080#, host_ip: "127.0.0.2"

    # If true, then any SSH connections made will enable agent forwarding.
    # Default value: false
    webserver.ssh.forward_agent = true

    webserver.vm.provision "ansible" do |ansible|
        ansible.playbook = "site.yml"
        # ansible.raw_arguments = ['--check']
        ansible.groups = {
          "webservers" => ["webserver"],
          "wordpress-servers" => ["webserver"],
          "database-servers" => ["webserver"]
        }
        ansible.extra_vars = {
          "ansible_ssh_user_home" => "/home/vagrant",
          "webmaster_pass" => "password",
          "webmaster_home" => "/home/webmaster",
          "webmaster_crypt_pass" => "$6$rounds=100000$5UstpnzLCZ4VIcQ9$lOixDksA/xNMYe6WxqFb0pFRRYwhfF28MeggovS/InsJoD9GixfRdM0kFXVa.t/kMrhWjOTXtf2mNowfxoUM1.",
          "wordpress_db_pass" => "weakpass",
          "wordpress_domain" => "wordpress.localhost",
          "wordpress_url" => "wordpress.localhost:8080",
          "wordpress_admin_user" => "admin",
          "wordpress_admin_pass" => "admin",
          "mysql_host" => "localhost",
          "mysql_root_user" => "root",
          "mysql_root_pass" => ""
        }
    end
  end
end
