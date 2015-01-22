# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "chef/centos-6.5"
  config.ssh.insert_key = false

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  IP_OFFSET = 100
  MASTERS_COUNT = 3
  SLAVES_COUNT = 2

  masters = []
  MASTERS_COUNT.times do |i|
    node_id = "mesosmaster#{i}"
    masters.push(node_id)
    config.vm.define node_id do |master|
      guest_ip = "192.168.50.#{IP_OFFSET + i + 1}"
      master.vm.network "private_network", ip: guest_ip
      master.vm.hostname = node_id

      if i == 0
        master.vm.network "forwarded_port", guest: 8080, host: 18080, protocol: 'tcp'
        master.vm.network "forwarded_port", guest: 4400, host: 14400, protocol: 'tcp'
        master.vm.network "forwarded_port", guest: 5050, host: 15050, protocol: 'tcp'
      end

    end
  end

  slaves = []
  SLAVES_COUNT.times do |i|
    node_id = "mesosslave#{i}"
    slaves.push(node_id)
    config.vm.define node_id do |slave|
      guest_ip = "192.168.50.#{IP_OFFSET + MASTERS_COUNT + i + 1}"
      slave.vm.network "private_network", ip: guest_ip
      slave.vm.hostname = node_id
      if i == SLAVES_COUNT - 1
        slave.vm.network "forwarded_port", guest: 5051, host: 15051, protocol: 'tcp'
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
