# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|
  CHEF_ROOT = './tools/chef'.freeze

  OS_NAME = 'centos-7.1'.freeze
  config.vm.box = 'bento/centos-7.1'

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = true

  config.omnibus.chef_version = :latest
  config.berkshelf.enabled = true
  config.berkshelf.berksfile_path = 'Berksfile.' + OS_NAME

  # Setup default vm
  config.vm.define 'default', primary: true do |node|
    node.vm.network :forwarded_port, guest: 80, host: 9090, auto_correct: true
    node.vm.network :private_network, ip: '10.0.0.10'
    node.vm.hostname = 'app.local'
    node.hostmanager.aliases = %w(
      html.local
    )
    node.vm.provider :virtualbox do |vb|
      vb.gui = false
      vb.memory = 4096
    end
    node.vm.synced_folder '.', '/var/www/app', disabled: true,
    :create => true, :owner=> 'www-data', :group => 'www-data'

    node.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = ['cookbooks', CHEF_ROOT + '/site-cookbooks']
      chef.custom_config_path = CHEF_ROOT + '/Vagrantfile.chef'
      chef.data_bags_path = CHEF_ROOT + '/data_bags'
      chef.roles_path = CHEF_ROOT + '/roles'
      chef.environments_path = CHEF_ROOT + '/environments'
      #chef.add_recipe 'httpd'

      # vagrant version < vagrant 1.8
      #json = JSON.parse(Pathname(__FILE__).dirname.join(CHEF_ROOT + '/nodes', 'centos-7.1.json').read)
      # vagrant version >= vagrant 1.8
      json = JSON.parse(File.read(Pathname(__FILE__).dirname.join(CHEF_ROOT + '/nodes', 'centos-7.1.json')))
      run_list = json.delete('run_list')
      chef.run_list = run_list
      #chef.run_list = ["recipe[netcommons]"]
      #chef.json = json
    end
  end

  config.vm.provision :hostmanager
  config.omnibus.chef_version = :latest
end
