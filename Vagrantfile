# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
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
    instance.vm.provision "ansible" do |ansible|
      ansible.limit = "controller"
      ansible.playbook = "setup_server.yml"
      ansible.groups = {
        "controller" => [ "ccabral" ],
      }
      ansible.host_vars = {
        "ccabral" => { "private_ip" => ccabral_ip }
      }
    end
  end
  NUMBER_OF_WORKERS = 1
  (1..NUMBER_OF_WORKERS).each do |id|
    worker_name = "worker-0#{id}"
    config.vm.define worker_name do |instance|
      instance.vm.hostname = worker_name + "-SW"
      instance.vm.network "private_network", ip: "192.168.42.111"
      instance.vm.provider "virtualbox" do |v|
        v.memory = 512
        v.cpus = 1
        v.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
        v.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
        v.customize ["modifyvm", :id, "--name", instance.vm.hostname ]
      end
      instance.vm.provision "ansible" do |ansible|
        ansible.limit = "all"
        ansible.playbook = "setup_worker.yml"
        ansible.groups = {
          "controller" => [ "ccabral" ],
          "workers" => [ worker_name ]
        }
        ansible.host_vars = {
          "ccabral" => { "private_ip" => ccabral_ip }
        }
      end
    end
  end
end
