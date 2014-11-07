Vagrant.configure('2') do |config|
  config.omnibus.chef_version = :latest

  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.scope = :box
  end

  config.vm.provider 'virtualbox' do |vb|
    vb.memory = 2048
    vb.cpus = 4
  end

  config.vm.define :precise64, primary: true do |ubuntu|
    ubuntu.vm.box = 'ubuntu/precise64'
  end

  config.vm.define :trusty64, autostart: false do |ubuntu|
    ubuntu.vm.box = 'ubuntu/trusty64'
  end

  config.vm.provision 'chef_solo' do |chef|
    chef.log_level      = :info
    chef.cookbooks_path = 'ci_environment'

    chef.roles_path = 'roles'
    chef.add_role 'worker_standard'
  end
end
