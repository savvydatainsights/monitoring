# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "savvydatainsights/ubuntu"
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "refresh-monitoring.yml"
    ansible.become = true
    ansible.host_vars = {
      "default" => {"ansible_python_interpreter" => "/usr/bin/python3"}
    }
    ansible.extra_vars = {
      target: "default"
    }
  end
end