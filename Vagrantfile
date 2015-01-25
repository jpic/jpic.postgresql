# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'fileutils'

Vagrant.require_version ">= 1.6.0"

$num_instances = 3
$enable_serial_logging = false
$vb_gui = false
$vb_memory = 1024
$vb_cpus = 1

Vagrant.configure(2) do |config|
  (1..$num_instances).each do |i|
    config.vm.define vm_name = "arch-%02d" % i do |config|
      config.vm.hostname = vm_name

      if $enable_serial_logging
        logdir = File.join(File.dirname(__FILE__), "log")
        FileUtils.mkdir_p(logdir)

        serialFile = File.join(logdir, "%s-serial.txt" % vm_name)
        FileUtils.touch(serialFile)

        config.vm.provider :virtualbox do |vb, override|
          vb.customize ["modifyvm", :id, "--uart1", "0x3F8", "4"]
          vb.customize ["modifyvm", :id, "--uartmode1", serialFile]
        end
      end

      config.vm.provider :virtualbox do |vb|
        vb.gui = $vb_gui
        vb.memory = $vb_memory
        vb.cpus = $vb_cpus
      end

      ip = "172.12.9.#{i+100}"
      config.vm.network :private_network, ip: ip

      config.vm.box = "terrywang/archlinux"

      config.vm.provision "ansible" do |ansible|
          ansible.playbook = "Vagrantplaybook.yml"
          ansible.inventory_path = "Vagrantinventory"
          ansible.verbose = 'vvvv'
          ansible.extra_vars = {
              ansible_python_interpreter: "/usr/bin/python2",
          }
      end
    end
  end
end
