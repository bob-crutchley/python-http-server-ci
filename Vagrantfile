# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'
guests = YAML.load_file("vagrant/hosts.yml")
Vagrant.configure(2) do |config|
  guests.each do |guest|
    config.vm.define guest['name'] do |guest_vm|
      set_box(guest, guest_vm)
      set_cpus_and_memory(guest, guest_vm)
      set_private_ip(guest, guest_vm)
      set_forwarded_ports(guest, guest_vm)
      update_with_package_manager(guest, guest_vm)
      install_packages(guest, guest_vm)
      run_install_scripts(guest, guest_vm)
    end
  end
end

def set_cpus_and_memory(guest, guest_vm)
  guest_vm.vm.provider "virtualbox" do |vb|
    vb.cpus = guest['cpus']
    vb.memory = guest['memory'] 
  end
end

def set_box(guest, guest_vm)
  guest_vm.vm.box = guest['box']
end

def set_private_ip(guest, guest_vm)
  guest_vm.vm.network "private_network", ip: guest['private_ip']
end

def set_forwarded_ports(guest, guest_vm)
  unless guest['forwarded_ports'].nil?
    guest['forwarded_ports'].each do |ports|
      guest_vm.vm.network "forwarded_port", guest: ports['guest'], host: ports['host']
    end
  end
end

def update_with_package_manager(guest, guest_vm)
  unless guest['package_manager'].nil?
    guest_vm.vm.provision "shell", inline: "sudo #{guest['package_manager']} update -y"
    if guest['package_manager'] == "apt"
      guest_vm.vm.provision "shell", inline: "sudo #{guest['package_manager']} upgrade -y"
    end
  end
end

def install_packages(guest, guest_vm)
  unless guest['packages'].nil?
    if guest['package_manager'] == "apk"
      guest_vm.vm.provision "shell", inline: <<-SHELL
        sudo #{guest['package_manager']} add #{guest['packages'].join(" ")}
      SHELL
    else
      guest_vm.vm.provision "shell", inline: <<-SHELL
        sudo #{guest['package_manager']} install -y #{guest['packages'].join(" ")}
      SHELL
    end
  end
end

def run_install_scripts(guest, guest_vm)
  unless guest['scripts'].nil?
    guest['scripts'].each do |script|
      guest_vm.vm.provision "shell", privileged: false, path: "vagrant/scripts/#{script}"
    end
  end
end

