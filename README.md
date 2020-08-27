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


