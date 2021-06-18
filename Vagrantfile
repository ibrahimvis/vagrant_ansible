# -*- mode: ruby -*-
# vi: set ft=ruby :

ssh_path = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
home_dir = File.expand_path('~').strip
memory = 2048
cpus = 2
num_of_vms = 4

Vagrant.configure("2") do |config|

  config.vagrant.plugins = "vagrant-libvirt"

  config.vm.box = "centos/7"
  config.vm.synced_folder './', '/vagrant', disabled: true
  
  config.vm.provider :libvirt do |domain|
    domain.memory = memory
    domain.cpus = cpus
    domain.disk_driver['cache'] = 'none'
    domain.management_network_name = 'ansible'
    domain.management_network_address = '192.168.99.0/24'
    domain.management_network_mode = 'nat'
    domain.management_network_autostart = true
  end

  (1..num_of_vms).each do |i|
    config.vm.define "mhost#{i}" do |server|
      server.vm.network :private_network, :ip => "192.168.99.#{i*10}"
      server.vm.hostname = "mhost#{i*10}.example.com"
      server.vm.provision "shell",
        inline: "echo #{ssh_path} >> /home/vagrant/.ssh/authorized_keys"
    end
  end


  config.trigger.after :up do |trigger|
    trigger.ruby do |env,machine|
      trigger.info = "Adding the host #{machine.name} to the ssh config file"
      system("echo -e 'Host #{machine.name}\n\tHostname #{machine.name}\n\tUser vagrant' >> #{home_dir}/.ssh/config")
    end
    
  end

end
