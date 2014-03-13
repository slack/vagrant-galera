# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

def configure_server(cluster_addresses)
  setup = <<-SCRIPT
export DEBIAN_FRONTEND=noninteractive
debconf-set-selections <<< 'mariadb-server-5.5 mysql-server/root_password password monty'
debconf-set-selections <<< 'mariadb-server-5.5 mysql-server/root_password_again password monty'

apt-get update
apt-get install -y software-properties-common python-software-properties
apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xcbcb082a1bb943db
add-apt-repository 'deb http://mirror.jmu.edu/pub/mariadb/repo/5.5/ubuntu precise main'

apt-get update
apt-get install -y rsync mariadb-galera-server-5.5 galera vim-nox

echo "
[mysqld]
query_cache_size=0
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
query_cache_type=0
bind-address=0.0.0.0

# Galera Provider Configuration
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster"
wsrep_cluster_address="gcomm://#{cluster_addresses.join(',')}"

# Galera Synchronization Configuration
wsrep_sst_method=rsync
" > /etc/mysql/conf.d/cluster.cnf
  SCRIPT
end

def configure_node(address)
  node = <<-SCRIPT
echo "[mysqld]
# Galera Node Configuration
wsrep_node_address=#{address}" > /etc/mysql/conf.d/node.cnf
  SCRIPT
end

CLUSTER_NODES = {
  "node-a" => "192.168.50.10",
  "node-b" => "192.168.50.20",
  "node-c" => "192.168.50.30"
}

# service mysql start --wsrep-new-cluster
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.forward_agent = true
  config.vm.box            = "opscode-ubuntu-12.04"

  config.cache.enable :apt # cachier

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024"]
  end

  # setup all of the galera nodes
  CLUSTER_NODES.each do |name, address|
    config.vm.define name do |node|
      node_address     = address
      node.vm.hostname = name
      node.vm.network :private_network, ip: address
      node.vm.provision "shell", inline: configure_server(CLUSTER_NODES.values)
      node.vm.provision "shell", inline: configure_node(address)

      node.vm.provision "shell", inline: "service mysql stop"
    end
  end
end
