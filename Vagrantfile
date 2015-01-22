# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "chef/centos-6.5"
  config.ssh.insert_key = false

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  MASTERS_COUNT = 2
  SLAVES_COUNT = 2

  masters = []
  MASTERS_COUNT.times do |i|
    node_id = "mesosmaster#{i}"
    masters.push(node_id)
    config.vm.define node_id do |master|
      master.vm.network "private_network", ip: "192.168.50.#{i + 1}"
    end
  end

  slaves = []
  SLAVES_COUNT.times do |i|
    node_id = "mesosslave#{i}"
    slaves.push(node_id)
    config.vm.define node_id do |slave|
      slave.vm.network "private_network", ip: "192.168.50.#{MASTERS_COUNT + i + 1}"
      if i == SLAVES_COUNT - 1
        slave.vm.provision :ansible do |ansible|
          ansible.playbook = 'playbook.yml'
          ansible.limit = 'all'
          ansible.groups = {
            "masters" => masters,
            "slaves" => slaves
          }
        end
      end
    end
  end
end
