# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "savvydatainsights/ubuntu"
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbooks/provision.yml"
    ansible.host_vars = {
      "default" => {"ansible_python_interpreter" => "/usr/bin/python3"}
    }
  end

  config.vm.provider "virtualbox" do |v|
    v.linked_clone = true if Gem::Version.new(Vagrant::VERSION) >= Gem::Version.new('1.8.0')
  end
end