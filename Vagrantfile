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
    webserver.vm.network "forwarded_port", guest: 80, host: 8080

    # If true, then any SSH connections made will enable agent forwarding.
    # Default value: false
    webserver.ssh.forward_agent = true

    webserver.vm.provision "ansible" do |ansible|
#        ansible.playbook = "ansible/site.yml"
        ansible.playbook = "ansible/site.yml"
        ansible.groups = {
          "webservers" => ["webserver"],
          "wordpress-servers" => ["webserver"],
          "database-servers" => ["webserver"]
        }
        ansible.extra_vars = {
          "domain" => "localhost",
          "fastcgi_listen" => "127.0.0.1:9000",
          "webmaster_pass" => "password",
          "webmaster_crypt_pass" => "$6$rounds=100000$5UstpnzLCZ4VIcQ9$lOixDksA/xNMYe6WxqFb0pFRRYwhfF28MeggovS/InsJoD9GixfRdM0kFXVa.t/kMrhWjOTXtf2mNowfxoUM1.",
          "wordpress_db_pass" => "weakpass"
        }
    end
  end
end
