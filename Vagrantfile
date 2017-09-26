# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

# for Windows
$script = <<SCRIPT

if ! [ `which ansible` ]; then
    yum update -y
    yum install -y http://ftp.iij.ad.jp/pub/linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
    sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/epel.repo
    yum install --enablerepo=epel -y ansible
fi
ansible-playbook -i /vagrant/provisioning/hosts /vagrant/provisioning/servers.yml

SCRIPT

Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  # CentOS6.6
  config.vm.box = "puppetlabs/centos-6.6-64-nocm"
  config.vm.box_url = "https://atlas.hashicorp.com/puppetlabs/boxes/centos-6.6-64-nocm/versions/1.0.3/providers/virtualbox.box"
  # CentOS7.x
  # config.vm.box = "puppetlabs/centos-7.2-64-nocm"
  # config.vm.box_url = "https://atlas.hashicorp.com/puppetlabs/boxes/centos-7.2-64-nocm/versions/1.0.1/providers/virtualbox.box"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  config.ssh.insert_key = false
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true

  # create web server
  config.vm.define :"nfs" do |host|
    host.vm.hostname = "nfs.dev"
    host.vm.network :private_network, ip: "192.168.34.212", netmask: "255.255.255.0"
    host.vm.network :private_network, ip: "192.168.33.212", virtualbox__intnet: "nfs"
    host.vm.network :forwarded_port, id: "ssh", guest: 22, host: 1920
    host.vm.synced_folder ".", "/vagrant", :mount_options => ["dmode=775", "fmode=664"]
    # ansible
    host.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/servers.yml"
      ansible.inventory_path = "provisioning/hosts"
      ansible.limit = 'all'
    end
    # for Windows
    # config.vm.provision "shell", inline: $script
  end
end
