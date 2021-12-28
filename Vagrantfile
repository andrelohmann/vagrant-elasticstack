# -*- mode: ruby -*-
# vi: set ft=ruby :

# load configs
require 'yaml'
current_dir    = File.dirname(File.expand_path(__FILE__))
configs        = YAML.load_file("#{current_dir}/config.yml")
vagrant_config = configs['configs']

#require necessary plugins
required_plugins = %w( vagrant-hostmanager vagrant-vbguest )
required_plugins.each do |plugin|
  system "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
end

Vagrant.configure(2) do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true

  config.vm.define "server" do |s|
    #s.vm.box = "ubuntu/bionic64" # 18.04
    s.vm.box = "ubuntu/focal64" # 20.04
    # set memory to 2048m
    s.vm.provider "virtualbox" do |vb|
      vb.memory = vagrant_config['server']['memory']
      vb.cpus = vagrant_config['server']['cpus']
      vb.customize ["modifyvm", :id, "--audio", "none"]
    end

    s.vm.synced_folder ".", "/vagrant", disabled: true
    s.vm.synced_folder "ansible_vagrant", "/vagrant/ansible_vagrant", create: true, owner: "vagrant", group: "vagrant", mount_options: ["dmode=775,fmode=775"]

    # auto update guest additions
    s.vbguest.auto_update = true

    # vagrant-hostmanager is necessary to update /etc/hosts on hosts and guests
    s.vm.network "private_network", ip: vagrant_config['server']['ip']
    s.vm.hostname = vagrant_config['server']['domain']
    s.hostmanager.aliases = vagrant_config['server']['aliases']

    # provision the vagrant machine using ansible
    # install ansible from its default repo
    s.vm.provision "shell", inline: "test -f .ssh/authorized_keys && cp --preserve=all .ssh/authorized_keys /tmp/authorized_keys" # preserve authorized_keys for `vagrant ssh` in case it already exists
    s.vm.provision "file", source: "~/.ssh", destination: ".ssh" # copy any key from the host; it is not possible to copy them as id_*, so other files might get overriden
    s.vm.provision "shell", inline: "mv /tmp/authorized_keys .ssh/" # restore any prior file
    s.vm.provision "shell", inline: "sudo apt install swapspace -y"

    # Run Ansible from the Vagrant VM
    if s.vm.box == "ubuntu/focal64"
      # install ansible from default repo
      s.vm.provision "shell", inline: "apt update && apt install ansible -y"
      s.vm.provision "ansible_local" do |ansible|
        ansible.install = false
        ansible.playbook = "ansible_vagrant/server-playbook.yml"
        ansible.galaxy_role_file = "ansible_vagrant/requirements.yml"
        #Uncomment when ansible 2.10 is available
        #ansible.galaxy_command = "sudo ansible-galaxy install -r %{role_file} --force; sudo ansible-galaxy collection install -r %{role_file} --force"
        ansible.extra_vars = {
          ansible_python_interpreter:"/usr/bin/python3"
        }
      end
    else
      s.vm.provision "ansible_local" do |ansible|
        ansible.install = true
        ansible.install_mode = :default
        ansible.playbook = "ansible_vagrant/server-playbook.yml"
        ansible.galaxy_role_file = "ansible_vagrant/requirements.yml"
        #Uncomment when ansible 2.10 is available
        #ansible.galaxy_command = "sudo ansible-galaxy install -r %{role_file} --force; sudo ansible-galaxy collection install -r %{role_file} --force"
        ansible.extra_vars = {
          ansible_python_interpreter:"/usr/bin/python3"
        }
      end
    end

    s.ssh.forward_agent = true
    s.ssh.forward_x11 = true
  end

  config.vm.define "client" do |c|
    #c.vm.box = "ubuntu/bionic64" # 18.04
    c.vm.box = "ubuntu/focal64" # 20.04
    # set memory to 2048m
    c.vm.provider "virtualbox" do |vb|
      vb.memory = vagrant_config['client']['memory']
      vb.cpus = vagrant_config['client']['cpus']
      vb.customize ["modifyvm", :id, "--audio", "none"]
    end

    c.vm.synced_folder ".", "/vagrant", disabled: true
    c.vm.synced_folder "ansible_vagrant", "/vagrant/ansible_vagrant", create: true, owner: "vagrant", group: "vagrant", mount_options: ["dmode=775,fmode=775"]

    # auto update guest additions
    c.vbguest.auto_update = true

    # vagrant-hostmanager is necessary to update /etc/hosts on hosts and guests
    c.vm.network "private_network", ip: vagrant_config['client']['ip']
    c.vm.hostname = vagrant_config['client']['domain']

    # provision the vagrant machine using ansible
    # install ansible from its default repo
    c.vm.provision "shell", inline: "test -f .ssh/authorized_keys && cp --preserve=all .ssh/authorized_keys /tmp/authorized_keys" # preserve authorized_keys for `vagrant ssh` in case it already exists
    c.vm.provision "file", source: "~/.ssh", destination: ".ssh" # copy any key from the host; it is not possible to copy them as id_*, so other files might get overriden
    c.vm.provision "shell", inline: "mv /tmp/authorized_keys .ssh/" # restore any prior file
    c.vm.provision "shell", inline: "sudo apt install swapspace -y"

    # Run Ansible from the Vagrant VM
    if c.vm.box == "ubuntu/focal64"
      # install ansible from default repo
      c.vm.provision "shell", inline: "apt update && apt install ansible -y"
      c.vm.provision "ansible_local" do |ansible|
        ansible.install = false
        ansible.playbook = "ansible_vagrant/client-playbook.yml"
        ansible.galaxy_role_file = "ansible_vagrant/requirements.yml"
        #Uncomment when ansible 2.10 is available
        #ansible.galaxy_command = "sudo ansible-galaxy install -r %{role_file} --force; sudo ansible-galaxy collection install -r %{role_file} --force"
        ansible.extra_vars = {
          ansible_python_interpreter:"/usr/bin/python3"
        }
      end
    else
      c.vm.provision "ansible_local" do |ansible|
        ansible.install = true
        ansible.install_mode = :default
        ansible.playbook = "ansible_vagrant/client-playbook.yml"
        ansible.galaxy_role_file = "ansible_vagrant/requirements.yml"
        #Uncomment when ansible 2.10 is available
        #ansible.galaxy_command = "sudo ansible-galaxy install -r %{role_file} --force; sudo ansible-galaxy collection install -r %{role_file} --force"
        ansible.extra_vars = {
          ansible_python_interpreter:"/usr/bin/python3"
        }
      end
    end

    c.ssh.forward_agent = true
    c.ssh.forward_x11 = true
  end
end
