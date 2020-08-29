# Example of Vagrant Ansible and Docker integration

This repository intend to shows integration between ansible and docker. Vagrant is used to provision local virtual server.

## Vagrant

As base image used Ububtu 18.04.

Configuration file: [Vagrantfile](Vagrantfile)

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

Ansible has next roles:
* python: to install python version 3.7 (or any other, defined by variable). It also installs pip and pip modules if defined
* docker: to install docker on host. Requires pip docker module
* redis: to install redis

### Roles

#### Mysql

Provisions docker container with mysql.

##### TODO

* Master and slave in Docker containers.
* First playbook run must result in replication process running with now errors.
* MySQL data directories must be persisted on host’s disk.

#### Redis

Provisions docker container with redis.

##### TODO

* Implement Ansible playbook to deploy Redis cluster in docker containers.
* After first playbook run, Redis cluster must be properly initialized.
* Redis data should be persistent on host.


#### RabbitMQ

Provisions docker container with RabbitMQ.

##### TODO
* Implement Ansible Playbook to deploy RabbitMQ cluster in docker containers.
* After first playbook run, RabbitMQ cluster must be properly initialized.
* Management interface must be present, admin username and password must be configurable via Ansible variables.
