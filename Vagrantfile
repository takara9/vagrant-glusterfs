# coding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # Ubuntu 16.04 
  config.vm.box = "ubuntu/xenial64"

  # GlusterFSのサーバーの構成と起動
  (1..3).each do |i|
    config.vm.define vm_name = "gluster#{i}" do |config|
      config.cache.scope = :box
      config.vm.hostname = vm_name
      
      private_ip = "172.20.1.#{i+21}"  # HostOnly IP addr
      config.vm.network "private_network", ip: private_ip
      config.vm.provider :virtualbox do |p|
        # CPU & RAM
        p.gui = false
        p.cpus = 1
        p.memory = 512
        # DISK
        vdisk = "vdisk/sdb-#{i}.vdi"
        # CREATE DISK
        if not File.exist?(vdisk) then
          p.customize [
            'createmedium', 'disk',
            '--filename', vdisk,
            '--format', 'VDI',
            '--size', 80 * 1024]
        end
        # ATTACH DISK
        p.customize [
          'storageattach', :id,
          '--storagectl', 'SCSI',
          '--port', 2,
          '--device', 0,
          '--type', 'hdd',
          '--medium', vdisk]
      end

      # 仮想サーバー起動後のGlusterFSのインストール
      config.vm.provision "shell", inline: <<-SHELL
         apt-get update && apt-get install -y software-properties-common
         apt-add-repository ppa:ansible/ansible
         apt-get update && apt-get install -y ansible
         ansible-playbook /vagrant/ansible-playbook/glusterfs.yml
      SHELL
    end
  end


  # Install Heketi & Configure  GlusterFS cluster
  config.vm.define vm_name = "heketi" do |config|
    config.cache.scope = :box
    config.vm.hostname = vm_name
    config.vm.network "private_network", ip: "172.20.1.21"
    
    config.vm.provision "shell", inline: <<-SHELL
      apt-get update && apt-get install -y software-properties-common
      apt-add-repository ppa:ansible/ansible
      apt-get update && apt-get install -y ansible
      ansible-playbook /vagrant/ansible-playbook/heketi-host.yml
      ansible-playbook /vagrant/ansible-playbook/heketi-node.yml
    SHELL
  end
end
