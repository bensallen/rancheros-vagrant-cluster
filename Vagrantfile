# -*- mode: ruby -*-
# vi: set ft=ruby :

require_relative 'vagrant_rancheros_guest_plugin.rb'

$number_of_nodes = 3
$docker_version = "17.03.2-ce"

Vagrant.configure("2") do |config|
  config.vm.box = "rancheros"
  config.vm.guest = :linux

  # Disable synced folder due to not being able to install VBox Extensions in busybox
  config.vm.synced_folder ".", "/vagrant", disabled: true

  (0..$number_of_nodes - 1).each do |i|
    hostname = "ros%02d" % i
    config.vm.define hostname do |node|
      node.vm.provider "virtualbox" do |vb|
        vb.check_guest_additions = false
        # If the host has a functional vboxsf filesystem
        vb.functional_vboxsf = false
        # Display the VirtualBox GUI when booting the machine
        # vb.gui = true
        # Customize the amount of memory on the VM:
        vb.memory = "2048"
      end
      ip = "172.19.8.#{i+100}"
      node.vm.network "private_network", ip: ip
      node.vm.hostname = hostname
      node.vm.provision "shell" do |s|
        s.inline = "sudo ros engine enable docker-$1"
        s.args = $docker_version
      end
      node.vm.provision "shell" do |s|
        s.inline = "sudo ros engine switch docker-$1"
        s.args = $docker_version
      end
    end
  end

  # SSH Configuration
  config.ssh.username = "rancher"
  config.ssh.keys_only = true

  # Stop vagrant-vbguest installing Guest Additions
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.no_install = true
  end
end
