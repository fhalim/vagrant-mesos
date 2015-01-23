# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "chef/centos-6.5"
  config.ssh.insert_key = false

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.memory = 512
  end

  IP_OFFSET = 100
  MASTERS_COUNT = 1
  SLAVES_COUNT = 2

  masters = []
  MASTERS_COUNT.times do |i|
    node_id = "mesosmaster#{i}"
    masters.push(node_id)
    config.vm.define node_id do |master|
      guest_ip = "192.168.50.#{IP_OFFSET + i + 1}"
      master.vm.network :private_network, ip: guest_ip
      master.vm.hostname = node_id
    end
  end

  slaves = []
  SLAVES_COUNT.times do |i|
    node_id = "mesosslave#{i}"
    slaves.push(node_id)
    config.vm.define node_id do |slave|
      guest_ip = "192.168.50.#{IP_OFFSET + MASTERS_COUNT + i + 1}"
      slave.vm.network :private_network, ip: guest_ip
      slave.vm.hostname = node_id
      if i == SLAVES_COUNT - 1
        # If at the last slave, kick off ansible provisioning for the cluster
        config.vm.provider :virtualbox do |vb|
          vb.customize ["modifyvm", :id, "--memory", "1024"]
        end
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
