# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # In general, it is a good idea to use latest release of Chef 11.x,
  # but please keep in mind that Travis team is provisioning with the version defined at
  # https://github.com/travis-ci/travis-images/blob/master/lib/travis/cloud_images/vm_provisioner.rb
  config.omnibus.chef_version = :latest

  config.vm.network "private_network", type: "dhcp"

  # Enable Vagrant plugins like vagrant-cachier and vagrant-vbguest
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
    config.cache.synced_folder_opts = {
        type: :nfs,
        mount_options: ['rw', 'vers=3', 'tcp', 'nolock', 'noatime', 'async']
    }
  end

  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = true
  end

  # VM tuning hints:
  # - A big VM (RAM > 1024M) is recommended to speed up compilation/install
  #   time
  # - A small VM (RAM < 1024M) is usually enough to verify a little set
  #   of isolated cookbooks
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 4
  end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # The Travis CI Linux worker is currently based on Ubuntu 12.04LTS 64bit
  # Pending: Refer to a basebox with Linux Kernel pinned on version 2.6
  config.vm.define :precise64, primary: true do |ubuntu|
    ubuntu.vm.box = "ubuntu/precise64"
  end

  config.vm.provision "shell",
    inline: "sudo apt-get update"

  config.vm.provision "chef_solo" do |chef|
    chef.log_level      = :info
    chef.cookbooks_path = "ci_environment"

    # Role-based Provisioning:
    chef.roles_path = "roles"
    chef.add_role "worker_standard"

    # php stuff
    # https://github.com/travis-ci/travis-images/blob/master/templates/worker.php.yml
    chef.add_recipe "php::multi"
    chef.add_recipe "composer"
    chef.add_recipe "sweeper"
  end

  config.vm.synced_folder '../xhprof', '/opt/xhprof',
      type: :nfs,
      mount_options: ['rw', 'vers=3', 'tcp', 'nolock', 'noatime', 'async']
end
