# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version ">= 2.2.0"

Vagrant.configure("2") do |config|
  config.vagrant.plugins = "vagrant-hostsupdater"

  config.vm.box = "savvydatainsights/ubuntu"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.hostname = "monitoring.savvydatainsights.co.uk"

  config.vm.provider "virtualbox" do |v|
    v.linked_clone = true
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbooks/provision.yml"
    ansible.host_vars = {
      "default" => {"ansible_python_interpreter" => "/usr/bin/python3"}
    }
  end
end