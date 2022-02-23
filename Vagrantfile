# -*- mode: ruby -*-
# vi: set ft=ruby :
 
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|
    # Every Vagrant development environment requires a box. You can search for
    # boxes at https://vagrantcloud.com/search.
    config.vm.define "server" do |server|
      server.vm.box = "ubuntu/focal64"
      server.vm.hostname = "server"
      server.vm.network "private_network", ip: "192.168.50.5"
      server.vm.provision "shell", inline: <<-SHELL
        sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/#g' /etc/ssh/sshd_config
        service ssh restart
      SHELL
    end

    config.vm.define "controlnode" do |controlnode|
      controlnode.vm.box = "ubuntu/focal64"
      controlnode.vm.hostname = "controlnode"
      controlnode.vm.network "private_network", ip: "192.168.50.4"
      controlnode.vm.synced_folder "./ansible","/home/vagrant/ansible"
    
      controlnode.vm.provision "shell", inline: <<-SHELL
        sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/#g' /etc/ssh/sshd_config
        service ssh restart
        sudo apt install -y software-properties-common
        sudo add-apt-repository --yes --update ppa:ansible/ansible
        sudo apt install -y ansible
        ssh-keyscan -H 192.168.50.5 >> ~/.ssh/known_hosts
      SHELL
    
      controlnode.vm.provision "ansible" do |ansible|
        ansible.inventory_path = "./ansible/hosts.ini"     
        ansible.playbook = "./ansible/playbook.yml"
        ansible.limit = ["all"]
        ansible.verbose = "v"
      end
    end
end
