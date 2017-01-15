# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.network :forwarded_port, guest: 8123, host: 8123

    config.vm.provider "virtualbox" do |vb, override|
        override.vm.box = "debian/jessie64"
        vb.memory = 512 # Increase to 1024 to simulate newer rpi's
        override.ssh.forward_agent = true
    end

    config.vm.define "vagrant-raspberry-pi", autostart: true do |server|
        server.vm.hostname = "vagrant-raspberry-pi"
        server.vm.synced_folder ".", "/vagrant"
        server.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible/playbooks/deploy/server.yml"
            ansible.inventory_path = "ansible/environments/development"
            ansible.verbose = "vvvv"
        end
    end
end
