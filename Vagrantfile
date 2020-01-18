# coding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  ## Gluster1 仮想マシンの起動
  #
  config.vm.define 'gluster1' do |machine|
    machine.vm.box = "ubuntu/bionic64"
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
           '--size', 1024000 ]
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
    machine.vm.synced_folder ".", "/vagrant", owner: "vagrant",
      group: "vagrant", mount_options: ["dmode=700", "fmode=700"]

    machine.vm.provision "shell", inline: <<-SHELL
sudo sed -i.bak -e "s%http://us.archive.ubuntu.com/ubuntu/%http://ftp.iij.ad.jp/pub/linux/ubuntu/archive/%g" /etc/apt/sources.list
SHELL

    ## GlusterFS インストール 
    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "ansible-playbook/glusterfs.yml"
      ansible.version        = "latest"
      ansible.verbose        = false
      ansible.install        = true
      ansible.limit          = "gluster1"
      ansible.inventory_path = "ansible-playbook/hosts"
    end
    
  end

  ## Gluster2 仮想マシンの起動
  #
  config.vm.define 'gluster2' do |machine|
    machine.vm.box = "ubuntu/bionic64"    
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
           '--size', 1024000 ]
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
    machine.vm.synced_folder ".", "/vagrant", owner: "vagrant",
      group: "vagrant", mount_options: ["dmode=700", "fmode=700"]

    machine.vm.provision "shell", inline: <<-SHELL
sudo sed -i.bak -e "s%http://us.archive.ubuntu.com/ubuntu/%http://ftp.iij.ad.jp/pub/linux/ubuntu/archive/%g" /etc/apt/sources.list
SHELL
    
    ## GlusterFS インストール 
    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "ansible-playbook/glusterfs.yml"
      ansible.version        = "latest"
      ansible.verbose        = false
      ansible.install        = true
      ansible.limit          = "gluster2"
      ansible.inventory_path = "ansible-playbook/hosts"
    end
    
  end

  ## Gluster3 仮想マシンの起動
  #
  config.vm.define 'gluster3' do |machine|
    machine.vm.box = "ubuntu/bionic64"
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
           '--size', 1024000 ]
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
    machine.vm.synced_folder ".", "/vagrant", owner: "vagrant",
      group: "vagrant", mount_options: ["dmode=700", "fmode=700"]

    machine.vm.provision "shell", inline: <<-SHELL
sudo sed -i.bak -e "s%http://us.archive.ubuntu.com/ubuntu/%http://ftp.iij.ad.jp/pub/linux/ubuntu/archive/%g" /etc/apt/sources.list
SHELL

    ## GlusterFS インストール 
    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "ansible-playbook/glusterfs.yml"
      ansible.version        = "latest"
      ansible.verbose        = false
      ansible.install        = true
      ansible.limit          = "gluster3"
      ansible.inventory_path = "ansible-playbook/hosts"
    end

  end
  
  ## Heketi サーバー
  #
  config.vm.define vm_name = "heketi" do |machine|
    machine.vm.box = "ubuntu/bionic64"
    machine.vm.hostname = vm_name
    machine.vm.network :private_network,ip: "172.20.1.20"
    #machine.vm.network :public_network, ip: "192.168.1.80", bridge: "en0: Ethernet"
    machine.vm.provider "virtualbox" do |vbox|
      vbox.gui = false        
      vbox.cpus = 1
      vbox.memory = 512
    end

    machine.vm.synced_folder ".", "/vagrant", owner: "vagrant",
      group: "vagrant", mount_options: ["dmode=700", "fmode=700"]


    ## GlusterFS インストール それぞれのノードに移動
    #
    #machine.vm.provision "ansible_local" do |ansible|
    #  ansible.playbook       = "ansible-playbook/glusterfs.yml"
    #  ansible.version        = "latest"
    #  ansible.verbose        = false
    #  ansible.install        = true
    #  ansible.limit          = "glusters"
    #  ansible.inventory_path = "ansible-playbook/hosts"
    #end
    
    machine.vm.provision "shell", inline: <<-SHELL
sudo sed -i.bak -e "s%http://us.archive.ubuntu.com/ubuntu/%http://ftp.iij.ad.jp/pub/linux/ubuntu/archive/%g" /etc/apt/sources.list
SHELL
    
    ## Heketiインストール 
    #
    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "ansible-playbook/heketi-host.yml"
      ansible.version        = "latest"
      ansible.verbose        = false
      ansible.install        = true
      ansible.limit          = "heketi"
      ansible.inventory_path = "ansible-playbook/hosts"
    end

    machine.vm.provision "ansible_local" do |ansible|
      ansible.playbook       = "ansible-playbook/heketi-node.yml"
      ansible.version        = "latest"
      ansible.verbose        = false
      ansible.install        = false
      ansible.limit          = "heketi"
      ansible.inventory_path = "ansible-playbook/hosts"
    end
  end
end
