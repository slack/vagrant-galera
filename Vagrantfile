# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

script = <<-SCRIPT
export DEBIAN_FRONTEND=noninteractive
debconf-set-selections <<< 'mariadb-server-5.5 mysql-server/root_password password monty'
debconf-set-selections <<< 'mariadb-server-5.5 mysql-server/root_password_again password monty'

apt-get update
apt-get install -y software-properties-common python-software-properties
apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xcbcb082a1bb943db
add-apt-repository 'deb http://mirror.jmu.edu/pub/mariadb/repo/5.5/ubuntu precise main'

apt-get update
apt-get install -y rsync mariadb-galera-server-5.5 galera
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.forward_agent = true

  config.vm.provision "shell", inline: script
  config.vm.box = "opscode-ubuntu-12.04"

  config.cache.scope = :box # cachier

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024"]
  end

  config.vm.define "nodea" do |node|
    node.vm.hostname = "node-a"
    node.vm.network "private_network", ip: "192.168.50.10"
  end

  config.vm.define "nodeb" do |node|
    node.vm.hostname = "node-b"
    node.vm.network "private_network", ip: "192.168.50.20"
  end
end
