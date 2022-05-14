# -*- mode: ruby -*-
# vi: set ft=ruby :

NUMBER_OF_WORKERS = 3
ControllerIP = "192.168.56.110"
Machines = {
  "Controllers" => [
    {
      "hostname" => "ccabral",
      "private_ip" => ControllerIP
    },
  ],
  "Workers" => (1..NUMBER_OF_WORKERS).map do |i|
    {
      "hostname" => "worker-%03u" % i,
      "private_ip" => "192.168.56.%u" % (110 + i),
    }
  end
}

NUMBER_OF_MACHINES = Machines.map { |key, group| group.length() }.sum(0)

i = 0
Vagrant.configure("2") do |config|
  config.vm.box = "centos/stream8"
  config.vm.box_version = "20210210.0"
  Machines.each do |groupname, group|
    group.each do |machine|
      i = i + 1
      config.vm.define machine["hostname"] do |instance|
        instance.vm.hostname = machine["hostname"]
        instance.vm.provider "virtualbox" do |v|
          v.memory = 1024
          v.cpus = 2
          v.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
          v.customize ["modifyvm", :id, "--name", machine["hostname"]]
        end
        instance.vm.network "private_network", ip: machine["private_ip"]
        if (i == NUMBER_OF_MACHINES)
          instance.vm.provision "ansible" do |ansible|
            ansible.limit = "all"
            ansible.playbook = "playbook.yml"
            ansible.groups = {
              "controller" => Machines["Controllers"].map { |x| x["hostname"] },
              "workers" => Machines["Workers"].map { |x| x["hostname"] }
            }
            ansible.host_vars = {
              "ccabral" => { "private_ip" => ControllerIP }
            }
          end
        end
      end
    end
  end
end
