# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'
VAGRANT_DEFAULT_PROVIDER = 'lxc'

def ansible(config, fqdn, domain, provider)
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provisioning/playbook.yml"
    ansible.host_key_checking = false
    ansible.extra_vars = {
      "sq_environment" => ENV['ENVIRONMENT'],
      "fqdn" => fqdn,
      "domain" => domain,
      "vagrant_provider" => provider
    }
    ansible.limit = 'all'
    # ansible.verbose = 'vvv'
  end
end

def virtualbox(config, fqdn, domain, ip)
  config.vm.provider :virtualbox do |vb, override|
    override.vm.box = 'ubuntu/trusty64'
    override.vm.network "private_network", ip: ip
    override.vm.synced_folder ".", "/vagrant", disabled: false
    override.vm.synced_folder ".", "/var/www/"
  end
end


def lxc(config, fqdn, domain)
  config.vm.provider :lxc do |lxc, override|
    override.vm.box = 'lxc-trusty64'
    override.vm.box_url = 'http://releases.crevette.pheromone.ca/lxc-trusty64.box'
    override.vm.synced_folder "sites/mtlnewtech", "/var/www/mtlnewtech"
    override.vm.synced_folder ".", "/vagrant", disabled: true
    ansible(override, fqdn, domain, 'lxc')
  end
end

def vsphere(config, fqdn, domain)
  config.vm.provider :vsphere do |vsphere, override|
    override.vm.box = 'dummy'
    override.vm.box_url = 'https://crevette.pheromone.ca/releases/vsphere-dummy.box'

    vsphere.host = "#{ENV['VSPHERE_HOST']}"
    vsphere.insecure = true
    vsphere.clone_from_vm = true 
    vsphere.template_name = "#{ENV['VSPHERE_TEMPLATE_NAME']}"

    vsphere.user = "#{ENV['VSPHERE_USER']}"
    vsphere.password = "#{ENV['VSPHERE_PASSWORD']}"
    override.ssh.private_key_path = "#{ENV['VSPHERE_SSH_PRIVATE_KEY']}"
    override.ssh.username = "#{ENV['VSPHERE_SSH_USERNAME']}"

    override.vm.synced_folder "sites/", "/var/www/", type: 'rsync', rsync__args: ["-a"]
    override.vm.synced_folder ".", "/vagrant", type: 'rsync', disabled: true

    ansible(override, fqdn, domain, 'vsphere')
  end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # ensure we have the needed environment variables.
  if ENV['ENVIRONMENT'].nil?
    ENV['ENVIRONMENT'] = 'local'
  end

  domain = "#{ENV['ENVIRONMENT']}.studioqi.ca"
  fqdn = "mtlnewtech-pycon.#{domain}"

  config.vm.define "web", primary: true do |machine|
    lxc(machine, fqdn, domain)
    vsphere(machine, fqdn, domain)
  end
end
