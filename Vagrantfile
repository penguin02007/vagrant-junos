# -*- mode: ruby -*-
# vi: set ft=ruby :

# ge-0/0/0.0 defaults to NAT for SSH + management connectivity
# over Vagrant's forwarded ports.  This should configure ge-0/0/1.0
# through ge-0/0/7.0 on VirtualBox.

#
# this Vagrantfile can and will wreak havoc on your VBox setup, so please
# use the Vagrant boxes at https://atlas.hashicorp.com/juniper unless you're
# attempting to extend this plugin (and can lose your VBox network config)
# TODO: launch VMs from something other than travis to CI all features
#
# Note: VMware can't name interfaces, but also supports 10 interfaces
# (through ge-0/0/9.0), so you should adjust accordingly to test
#
# Note: interface descriptions in Junos don't work yet, but you woud set them
# here with 'description:'.
required_plugins = %w(vagrant-ssh vagrant-scp vagrant-host-shell vagrant-junos)

plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }
if not plugins_to_install.empty?
  puts "Installing plugins: #{plugins_to_install.join(' ')}"
  if system "vagrant plugin install #{plugins_to_install.join(' ')}"
    exec "vagrant #{ARGV.join(' ')}"
  else
    abort "Installation of one or more plugins has failed. Aborting."
  end
end

Vagrant.configure(2) do |config|
  config.vm.box = "juniper/ffp-12.1X47-D15.4-packetmode"
  config.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
      vb.gui = false
      vb.linked_clone = true
      vb.customize ["modifyvm", :id, "--nicpromisc2", "deny"]
  end

  config.vm.define "vsrx1" do |v|
    v.vm.host_name = "vsrx1"
    v.vm.network "private_network",
        ip: "192.168.5.10",
        virtualbox__intnet: "mgmt"
    v.vm.network "private_network",
        virtualbox__intnet: "vsrx1-r1"
  end

  config.vm.define "vsrx2" do |v|
    v.vm.host_name = "vsrx2"
    v.vm.network "private_network",
        ip: "192.168.5.11",
        virtualbox__intnet: "mgmt"
    v.vm.network "private_network",
        virtualbox__intnet: "vsrx2-r1"
  end

  config.vm.define "client1" do |v|
    v.vm.box = "ubuntu/xenial64"
    v.vm.hostname = "client1"
    v.vm.network "private_network",
        ip: "192.168.5.9",
        virtualbox__intnet: "mgmt"
  end

end
