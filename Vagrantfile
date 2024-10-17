# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "debian/bookworm64"
  #General provision
  config.vm.provision "shell", inline: <<-script
      apt-get update
      apt-get upgrade -y
      apt-get install bind9 dnsutils -y
      script
  #First DNS server
  config.vm.define "master" do |master|
    master.vm.network  "private_network",
     ip: "192.168.57.103"
    master.vm.hostname= "tierra.sistema.test"
    master.vm.provision "shell", name: "dns_config_master", inline: <<-shell
      cp /vagrant/master/named /etc/default/
      cp /vagrant/master/named.conf.options /etc/bind/ 
      cp /vagrant/master/named.conf.local /etc/bind/
      systemctl restart named
      shell
  end

  # Slave DNS server
  # config.vm.define "slave" do |slave|
  #   slave.vm.network  "private_network",
  #    ip: "192.168.57.102"
  #   slave.vm.hostname= "tierra.sistema.test"
  #   slave.vm.provision "shell", name: "dns_config_slave", inline: <<-shell
  #     cp /vagrant/slave/named /etc/default/
  #     systemctl restart named
  #     shell
  # end
end
