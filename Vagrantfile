# -*- mode: ruby -*-
# vi: set ft=ruby :

# Before use:
# vagrang plugin install vagrant-proxyconf

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
    # The most common configuration options are documented and commented below.
    # For a complete reference, please see the online documentation at
    # https://docs.vagrantup.com.
  
    config.proxy.http = "http://10.1.0.99:3142"
    config.proxy.https = "http://10.1.0.99:3142"
    config.proxy.no_proxy = "localhost,127.0.0.1"
   
    config.vm.provider :hyperv do |provider|
      provider.cpus = "2"
      provider.memory = "1024"
      provider.vm_integration_services = {
        guest_service_interface: true,
        time_synchronization: true
      }
    end
  
    # https://docs.ansible.com/ansible/latest/user_guide/index.html
    
    # Provision Znuny development vm
    config.vm.define "znuny-dv", primary: true do |znunydv|
      znunydv.vm.box = "generic/debian10"
      znunydv.vm.network "private_network", ip: "10.0.0.11"
      znunydv.vm.network "forwarded_port", guest: 80, host: 8081
      znunydv.vm.hostname = "znuny-dv.localhost"
      # znunydv.vm.synced_folder ".", "/home/vagrant/otrs-dynamicfieldhtmltext", type: "smb"
      znunydv.vm.provision "file", source: "./Custom", destination: "/home/vagrant/otrs-dynamicfieldhtmltext/"
      znunydv.vm.provision "file", source: "./Kernel", destination: "/home/vagrant/otrs-dynamicfieldhtmltext/"
      znunydv.vm.provision "file", source: "./var", destination: "/home/vagrant/otrs-dynamicfieldhtmltext/"
      znunydv.vm.provision "file", source: "./playbook.znuny-dv.yml", destination: "/home/vagrant/"
      znunydv.vm.provision "shell", 
        inline: 'printf "\ndeb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main" | sudo tee -a /etc/apt/sources.list'
      znunydv.vm.provision "shell", inline: "sudo apt-get install -y gnupg"
      znunydv.vm.provision "shell", inline: "sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys --keyserver-options http-proxy=$http_proxy 93C4A3FD7BB9C367"
      znunydv.vm.provision "shell", inline: "sudo apt-get update && sudo apt-get install -y ansible"
      znunydv.vm.provision "shell", inline: "sudo ansible-galaxy collection install community.mysql"
      znunydv.vm.provision "ansible_local" do |ansible|
        ansible.playbook = "playbook.znuny-dv.yml"
        ansible.provisioning_path = "/home/vagrant"
        ansible.host_vars = {
          "znuny-dv" => {"ansible_python_interpreter" => "/usr/bin/python3"}
        }
        ansible.verbose = "-vvv"        
      end
      # znunydv.vm.provision "ansible" do |ansible|
      #   ansible.playbook = "playbook.znuny-dv.yml"
      #   ansible.host_vars = {
      #     "znuny-dv" => {"ansible_python_interpreter" => "/usr/bin/python3"}
      #   }
      #   ansible.verbose = "-vvv"
      # end
    end  
  
  end