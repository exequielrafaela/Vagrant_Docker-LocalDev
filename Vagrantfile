# -*- mode: ruby -*-
# vi: set ft=ruby :

# Check Vagrant dependencies
if !Vagrant.has_plugin?("vagrant-docker-compose")
      system('vagrant plugin install vagrant-docker-compose')

     raise("vagrant-docker-compose installed. Run command \"vagrant up\" again.");
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.require_version '>= 1.8.0'
Vagrant.configure(2) do |config|

  # Assign additional private network, w/ auto-configure and DHCP
  config.vm.network "private_network", type: "dhcp"
  config.hostmanager.enabled = true
  config.hostmanager.ip_resolver = proc do |vm, resolving_vm|
    if vm.id
        `VBoxManage guestproperty get #{vm.id} "/VirtualBox/GuestInfo/Net/1/V4/IP"`.split()[1]
    end
  end

  # Create and configure the VM
  config.vm.define :devdockerserver do |srv|
    srv.vm.hostname = "dev-docker-server"
    srv.vm.box = "ubuntu/trusty64"
    srv.vm.synced_folder ".", "/lemp_docker_demo", create: true
    # Provision with Docker Engine + docker-compose
    srv.vm.provision :docker
    srv.vm.provision :docker_compose, yml: "/lemp_docker_demo/docker-compose.yml", rebuild: true, run: "always"
    srv.vm.provision :shell do |shell|
      shell.inline = "sudo groupadd docker"
      shell.inline = "sudo usermod -aG docker vagrant"
      shell.inline = "sudo curl -L https://github.com/docker/machine/releases/download/v0.8.2/docker-machine-`uname -s`-`uname -m` > /usr/local/bin/docker-machine &&
      sudo chmod +x /usr/local/bin/docker-machine"
      shell.inline = "sudo apt-get update"
      shell.inline = "sudo apt-get install -y npm"
      shell.inline = "sudo npm install azure-cli -g"
      shell.inline = "sudo ln -s /usr/bin/nodejs /usr/bin/node"
    end
    # Configure CPU & RAM per settings
    srv.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
    end
  end
end
