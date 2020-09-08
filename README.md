# Example of Vagrant Ansible and Docker integration

This repository intend to show integration between ansible and docker. Vagrant is used to provision local virtual server.

## Vagrant

As base image used Ububtu 18.04.

Configuration file: [Vagrantfile](Vagrantfile)

Added workaround for python-jmespath.

### Ports

Vagrant has next ports forwarded:

* 15672: RabbitMQ Management UI from Primary RabbitMQ docker container. Please use credentials configured in [ansible](#Variables) variables.
* 15673: RabbitMQ Management UI from Secondary RabbitMQ docker container. Please use credentials configured in [ansible](#Variables) variables.
* 7000: Redis-1 node from [redis](#Redis) role.
* 7001: Redis-2 node from [redis](#Redis) role.
* 7002: Redis-3 node from [redis](#Redis) role.

### Commands

To start VM run next command:
```
vagrant up
```
Please keep in mind that on start VM will be provisioned by [ansible](#Ansible)

To stop VM:

```
vagrant halt
```

To destroy VM:
```
vagrant destroy
```

To provision/reprovision VM:
```
vagrant provision
```

## Ansible

Main configuration file [setup.yml](setup.yml)

### Global variables

Global variables used in ansible playbook so it's easier to find or update it. Some roles have additional variables defined inside role.

* python_version: python version to install on host machine.
* pip: name of python pip module package.
* ansible_python_interpreter: version of python to use by ansible. Some ansible modules require python3.
* docker_network: name of docker network. All containters will be started using the same docker network.
* rabbitmq_user: RabbitMQ admin username to access Management UI.
* rabbitmq_password: RabbitMQ admin password to access Management UI.
* redis_nodes: amount of redis nodes to start. Please note that they will be configured as cluster.
* mysql_servers: amount of mysql nodes to start. First one will be master, others - slaves.
* mysql_root_password: mysql root password configured by ansible for mysql docker container.


### Roles

Ansible has next roles:
* python: to install python version 3.7 (or any other, defined by variable). It also installs pip and pip modules if defined
* docker: to install docker on host. Requires pip docker module
* [redis](#Redis)
* [mysql](#Mysql)
* [rabbitmq](#RabbitMQ)

#### Redis

Provisions docker container with redis.

Few notes:
* redis version 6 requires at least 3 nodes to setup cluster
* redis-cli used to initialize redis cluster
* Ubuntu 18.04 has redis-cli version 4.0 which doesn't support cluster command
* redis-cli executed inside redis_1 docker container
* redis-cli requires IP addresses (not a hostname!) to initialize redis cluster - [opened issue](https://github.com/redis/redis/issues/2186)

#### RabbitMQ

Provisions docker containers with RabbitMQ. Two docker containers: primary and secondary. They works together as cluster. Used `lucifer8591/rabbitmq-server:3.7.17` docker image for testing purposes.

To access Admin UI, enter [http://localhost:15672/](http://localhost:15672/)  in your local browser. User and password defined [here](setup.yml)

#### Mysql

Provisions docker container with mysql. Only one docker container. No replication or clustering.

Role taken from [here](https://github.com/nethalo/ansible-mysql-docker), with small updates.

Few notes:
* role with name mysqlv2 is used. role with name mysql is my try to create this role from scratch, however faced problems, so to save time used role from github.
* this role has variable with name `mysql_port_prefix` . Idea of this variable - to have mysql nodes with ports prefix+1, prefix+2, for example 13301, 13302. So it's easier to access service.
* in order to verify master-slave replication, you will need to ssh into vagrant via `vagrant ssh`, next access slave via `mysql -u root -h 127.0.0.1 -P 13302` , and then run next SQL query `SHOW SLAVE STATUS;`
* also as additional step ansible creates user root@%, so it's possible to access mysql master via `mysql -u root -h 127.0.0.1 -P 3307 -p` using password from [here](setup.yml) and then run next SQL query `SHOW SLAVE STATUS;`
* percona mysql images runs as user `mysql` with uid `1001`.


## TODO
* add check if mysql is already in master-slave mode. As for now playbook fail on second run.
* add check if redis nodes already in cluster. Now playbook will fail on second run.