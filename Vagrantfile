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
  config.vm.define "tierra" do |master|
    master.vm.network  "private_network",
     ip: "192.168.57.103"
    master.vm.hostname = "tierra.sistema.test"
    master.vm.provision "shell", name: "dns_config_master", inline: <<-shell
      cp -v /vagrant/master/named /etc/default/
      cp -v /vagrant/master/named.conf.options /etc/bind/ 
      cp -v /vagrant/master/named.conf.local /etc/bind/
      cp -v /vagrant/master/db.192.168.57.dns /var/lib/bind/
      cp -v /vagrant/master/db.sistema.test.dns /var/lib/bind/
      chown :bind /var/lib/bind/*.dns
      systemctl restart named
      shell
  end

  # Slave DNS server
  config.vm.define "venus" do |slave|
    slave.vm.network  "private_network",
     ip: "192.168.57.102"
    slave.vm.hostname= "venus.sistema.test"
    slave.vm.provision "shell", name: "dns_config_slave", inline: <<-shell
      cp -v /vagrant/slave/named /etc/default/
      cp -v /vagrant/slave/named.conf.local /etc/bind/
      cp -v /vagrant/slave/named.conf.options /etc/bind/ 
      systemctl restart named
      shell
  end
end
