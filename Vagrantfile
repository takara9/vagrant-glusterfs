# coding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  ## Gluster1 仮想マシンの起動
  #
  config.vm.define 'gluster1' do |machine|
    machine.vm.box = "ubuntu/xenial64"
    machine.vm.hostname = 'gluster1'
    machine.vm.network :private_network,ip: "172.20.1.21"
    #machine.vm.network :public_network, ip: "192.168.1.81", bridge: "en0: Ethernet"
    machine.vm.provider "virtualbox" do |vbox|
      vbox.gui = false        
      vbox.cpus = 1
      vbox.memory = 512
      # DISK
      vdisk = "vdisk/sdb-1.vdi"
      # CREATE DISK
      if not File.exist?(vdisk) then
         vbox.customize [
           'createmedium', 'disk',
           '--filename', vdisk,
           '--format', 'VDI',
           '--size', 80 * 1024]
      end
      # ATTACH DISK
      vbox.customize [
        'storageattach', :id,
        '--storagectl', 'SCSI',
        '--port', 2,
        '--device', 0,
        '--type', 'hdd',
        '--medium', vdisk]
    end
    machine.vm.provision "shell", inline: <<-EOF
      apt-get update && apt-get install -y python-minimal
    EOF
  end

  ## Gluster2 仮想マシンの起動
  #
  config.vm.define 'gluster2' do |machine|
    machine.vm.box = "ubuntu/xenial64"
    machine.vm.hostname = 'gluster2'
    machine.vm.network :private_network,ip: "172.20.1.22"
    #machine.vm.network :public_network, ip: "192.168.1.82", bridge: "en0: Ethernet"
    machine.vm.provider "virtualbox" do |vbox|
      vbox.gui = false        
      vbox.cpus = 1
      vbox.memory = 512
      # DISK
      vdisk = "vdisk/sdb-2.vdi"
      # CREATE DISK
      if not File.exist?(vdisk) then
         vbox.customize [
           'createmedium', 'disk',
           '--filename', vdisk,
           '--format', 'VDI',
           '--size', 80 * 1024]
      end
      # ATTACH DISK
      vbox.customize [
        'storageattach', :id,
        '--storagectl', 'SCSI',
        '--port', 2,
        '--device', 0,
        '--type', 'hdd',
        '--medium', vdisk]
    end
    machine.vm.provision "shell", inline: <<-EOF
      apt-get update && apt-get install -y python-minimal
    EOF
  end

  ## Gluster3 仮想マシンの起動
  #
  config.vm.define 'gluster3' do |machine|
    machine.vm.box = "ubuntu/xenial64"
    machine.vm.hostname = 'gluster3'
    machine.vm.network :private_network,ip: "172.20.1.23"
    #machine.vm.network :public_network, ip: "192.168.1.83", bridge: "en0: Ethernet"
    machine.vm.provider "virtualbox" do |vbox|
      vbox.gui = false        
      vbox.cpus = 1
      vbox.memory = 512
      # DISK
      vdisk = "vdisk/sdb-3.vdi"
      # CREATE DISK
      if not File.exist?(vdisk) then
         vbox.customize [
           'createmedium', 'disk',
           '--filename', vdisk,
           '--format', 'VDI',
           '--size', 80 * 1024]
      end
      # ATTACH DISK
      vbox.customize [
        'storageattach', :id,
        '--storagectl', 'SCSI',
        '--port', 2,
        '--device', 0,
        '--type', 'hdd',
        '--medium', vdisk]
    end
    machine.vm.provision "shell", inline: <<-EOF
      apt-get update && apt-get install -y python-minimal
    EOF
  end
  
  ## Heketi サーバー
  #
  config.vm.define vm_name = "heketi" do |machine|
    machine.vm.box = "ubuntu/xenial64"
    machine.vm.hostname = vm_name
    machine.vm.network :private_network,ip: "172.20.1.20"
    #machine.vm.network :public_network, ip: "192.168.1.80", bridge: "en0: Ethernet"
    machine.vm.provider "virtualbox" do |vbox|
      vbox.gui = false        
      vbox.cpus = 1
      vbox.memory = 512
    end
    
    ## GlusterFS インストール 
    #
    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "ansible-playbook/glusterfs.yml"
      ansible.version        = "2.6.3"
      ansible.verbose        = false
      ansible.install        = true
      ansible.limit          = "glusters"
      ansible.inventory_path = "ansible-playbook/hosts"
    end

    ## Heketiインストール 
    #
    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "ansible-playbook/heketi-host.yml"
      ansible.version        = "2.6.3"
      ansible.verbose        = false
      ansible.install        = true
      ansible.limit          = "heketi"
      ansible.inventory_path = "ansible-playbook/hosts"
    end

    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "ansible-playbook/heketi-node.yml"
      ansible.version        = "2.6.3"
      ansible.verbose        = false
      ansible.install        = true
      ansible.limit          = "heketi"
      ansible.inventory_path = "ansible-playbook/hosts"
    end
  end
end
