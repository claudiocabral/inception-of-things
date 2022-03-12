# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  #config.vm.provision "shell", inline: <<-SHELL
  #  yum install -y net-tools git tar
  #SHELL
  ccabral_ip = "192.168.42.110"
  config.vm.define "ccabral" do |instance|
    instance.vm.hostname = "ccabralS"
    instance.vm.provider "virtualbox" do |v|
      v.memory = 512
      v.cpus = 1
      v.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
      v.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
      v.customize ["modifyvm", :id, "--name", "ccabralS"]
    end
    instance.vm.network "private_network", ip: ccabral_ip
  end
  config.vm.provision "shell", inline: <<-SHELL
    yum install -y net-tools git tar
  SHELL
  config.vm.define "worker-01" do |instance|
    instance.vm.hostname = "worker-01-SW"
    instance.vm.network "private_network", ip: "192.168.42.111"
    instance.vm.provider "virtualbox" do |v|
      v.memory = 512
      v.cpus = 1
      v.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
      v.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
      v.customize ["modifyvm", :id, "--name", "worker-01-SW"]
    end
  end
  config.vm.provision "ansible" do |ansible|
    ansible.limit = "all"
    ansible.playbook = "playbook.yml"
    ansible.groups = {
      "controller" => ["ccabral"],
      "workers" => ["worker-01"],
    }
    ansible.host_vars = {
      "ccabral" => { "private_ip" => ccabral_ip }
    }
  end
end
