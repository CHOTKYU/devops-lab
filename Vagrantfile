# -*- mode: ruby -*-
# vi: set ft=ruby :

VM_IP = "192.168.56.10"

Vagrant.configure("2") do |config|

  
  config.vm.box = "debian/bookworm64"

  
  config.vm.hostname = "devops-lab"

  
  config.vm.network "private_network", ip: VM_IP

  
  config.vm.provider "virtualbox" do |vb|
    vb.name   = "devops-lab"
    vb.memory = "4096"   
    vb.cpus   = 2
  end

  
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update -qq
    apt-get install -y -qq python3 ansible
  SHELL

  
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook       = "provision/playbook.yml"
    ansible.inventory_path = "provision/inventory.ini"
    ansible.limit          = "all"
    ansible.extra_vars     = { vm_ip: VM_IP }
  end

end