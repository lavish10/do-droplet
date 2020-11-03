# -*- mode: ruby -*-
# vi: set ft=ruby :

require_relative('utils')

def create_vms_on_digital_ocean(machines, config)
  cluster_name_prefix = ENV.fetch('CLUSTER_NAME_PREFIX', 'k8s')
  machines.each_with_index do |machine, index|
    config.vm.define machine_name(machine) do |node|
      node.vm.provider :digital_ocean do |provider, override|
        provider.ssh_key_name = ENV["DIGITAL_OCEAN_SSH_KEY_NAME"]
        provider.token = ENV["DIGITAL_OCEAN_TOKEN"]
        provider.image = 'ubuntu-18-04-x64'
        provider.region = 'blr1'
        provider.size = ENV.fetch("INSTANCE_TYPE", 's-1vcpu-1gb')
        provider.tags =  ['kubernetes', machine[:role], cluster_name_prefix]
        provider.private_networking = true

        override.vm.synced_folder "src/", "/srv", disabled: true
        override.vm.synced_folder ".", "/vagrant", disabled: true
        override.ssh.private_key_path = "~/.ssh/#{ENV["DIGITAL_OCEAN_PRIVATE_KEY"]}"
        override.vm.box = 'digital_ocean'
        override.vm.box_url = "https://github.com/devopsgroup-io/vagrant-digitalocean/raw/master/box/digital_ocean.box"
        override.nfs.functional = false

        configure_provision(index, machines, override, {
          "network_interface": "eth1",
          "provider": "digitalocean",
          "digital_ocean_token": ENV["DIGITAL_OCEAN_TOKEN"],
        })
      end
    end
  end
end

def create_vms(machines, config)
  ENV.fetch("K8S_PROVIDER", "virtualbox") == "virtualbox" ? :create_vms_on_virtualbox : :create_vms_on_digitalocean
end

Vagrant.configure("2") do |config|
  machines = [
    {"name": "master", "virtualbox_private_ip": "10.10.10.2", "role": "master"}
  ]

  provider = ENV.fetch("K8S_PROVIDER", "virtualbox")
  create_vms_on_virtualbox(machines, config) if provider == "virtualbox"
  create_vms_on_digital_ocean(machines, config) if provider == "digital_ocean"
end
