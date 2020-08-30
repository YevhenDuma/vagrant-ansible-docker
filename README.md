# Example of Vagrant Ansible and Docker integration

This repository intend to show integration between ansible and docker. Vagrant is used to provision local virtual server.

## Vagrant

As base image used Ububtu 18.04.

Configuration file: [Vagrantfile](Vagrantfile)

### Ports

Vagrant has next ports forwarded:

* 15672: RabbitMQ Management UI from Primary RabbitMQ docker container. Please use credentials configured in [ansible](#Variables) variables.
* 15673: RabbitMQ Management UI from Secondary RabbitMQ docker container. Please use credentials configured in [ansible](#Variables) variables.


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

## Ansible

Main configuration file [setup.yml](setup.yml)

### Variables

* python_version: python version to install on host machine.
* pip: name of python pip module package
* pip_modules: name of pip modules to install. List separated by spaces
* ansible_python_interpreter: version of python to use by ansible. Some ansible modules require python3
* mysql_root_password: mysql root password configured by ansible for mysql docker container
* docker_network: name of docker network. All containters will be started using the same docker network
* rabbitmq_user: RabbitMQ admin username to access Management UI
* rabbitmq_password: RabbitMQ admin password to access Management UI

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
* from version 6(?) redis requires 3 nodes to setup cluster
* I am using redis-cli to initialize redis cluster
* Ubuntu 18.04 has redis-cli version 4.0 wcich doesn't support cluster command
* redis-cli executed from redis docker container
* redis-cli requires IP addresses (not a hostname!) to initialize redis cluster

#### RabbitMQ

Provisions docker containers with RabbitMQ. Two docker containers: primary and secondary. They works together as cluster. Used `lucifer8591/rabbitmq-server:3.7.17` docker image for testing purposes.

#### Mysql

Provisions docker container with mysql.

##### TODO

* Master and slave in Docker containers.
* First playbook run must result in replication process running with now errors.
* MySQL data directories must be persisted on host’s disk.