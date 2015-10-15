require 'fileutils'

# Vagrantfile API/syntax version.
VAGRANTFILE_API_VERSION = "2"

### Check if required plugins are installed
unless Vagrant.has_plugin?("vagrant-bindfs")
  raise 'vagrant-bindfs plugin is missing!'
end

unless Vagrant.has_plugin?("vagrant-hostsupdater")
  raise 'vagrant-hostsupdater plugin is missing!'
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

# ###################################################################################################################################################
# UBUNTU
# ###################################################################################################################################################

 config.vm.define :ubuntu do |node|
  node.vm.box = "ubuntu1404-64"
  node.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
  node.vm.network "private_network", ip: "192.168.200.200"
  node.vm.hostname = "mysite"
  node.bindfs.debug = true

### Set a right amount of memory
  node.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024"]
  end

### Configure synced folders with bindfs
  node.vm.synced_folder "./mysite.dev", "/vagrant-nfs", nfs: true
  node.bindfs.bind_folder "/vagrant-nfs", "/var/www/mysite.dev"

  node.vm.synced_folder "./api.mysite.dev", "/vagrant-nfs-3", nfs: true
  node.bindfs.bind_folder "/vagrant-nfs-3", "/var/www/api.mysite.dev"

### Domain where you will reach WordPress
  node.hostsupdater.aliases = [
    "mysite.dev",
    "api.mysite.dev"
  ]

### Fix for Ansible bug resulting in an encoding error
  ENV['PYTHONIOENCODING'] = "utf-8"

### Run Ansible provision
  node.vm.provision "ansible" do |ansible|
    ansible.limit = 'all'
    ansible.verbose = "" ### If you notice errors, add "vvv" here to activate verbose mode
    ansible.playbook = "ansible/development.yml"
    ansible.inventory_path = "ansible/hosts"
  end
 end
end
