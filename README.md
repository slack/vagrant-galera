Vagrantfile + shell provisioners to get a basic 3 node Galera cluster up and running.

I'm using this for testing and experimentation.

Host addressing:

* node-a: 192.168.50.10
* node-b: 192.168.50.20
* node-c: 192.168.50.30

The cluster is not automatically setup, you need to:

1. copy the /etc/mysql/debian.cnf file to node-b and node-c (should probably figure out if you can specify passwords with debconf)
2. start node-a mysql with 'service mysql start --wsrep-new-cluster'
3. start node-b and node-c with 'service mysql start'
