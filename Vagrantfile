# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.box = "ubuntu/bionic64"
    config.vm.network :private_network, ip: "192.168.58.111"
    config.vm.network "forwarded_port", guest: 15672, host: 15672
    config.vm.network "forwarded_port", guest: 15673, host: 15673
    config.vm.network "forwarded_port", guest: 7001, host: 7001
    config.vm.network "forwarded_port", guest: 13301, host: 3306
    config.vm.network "forwarded_port", guest: 13302, host: 3307

    config.vm.provider :virtualbox do |vb|
        vb.customize [
            'modifyvm', :id,
            '--natdnshostresolver1', 'on',
            '--memory', '4096',
            '--cpus', '2'
        ]
    end
    config.vm.provision "ansible_local" do |a|
        #a.verbose = "vvv"
        a.playbook = "setup.yml"
    end
end