# -*- mode: ruby -*-
# vi: set ft=ruby :

# This Vagrantfile needs the vagrant-omnbius plugin installed
# To install this, run "vagrant plugin install vagrant-omnibus"

Vagrant.configure("2") do |config|
  config.vm.provider :virtualbox do |vbox|
    vbox.customize ['modifyvm', :id,
                    '--memory', 1024]
  end

  config.vm.box = "Chef-CentOS-6.5"
  config.vm.box_url = "http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.5_chef-provisionerless.box"

  config.vm.hostname = "zabbix-berkshelf"
  server_ip = "192.168.50.10"
  config.vm.network :private_network, ip: server_ip

  # (Workaround) Enable ssh public-key login.
  config.ssh.username = "vagrant"
  config.ssh.password = "vagrant"
  config.ssh.insert_key = true
  config.vm.provision "shell", inline: "
    sed -i 's/#PubkeyAuthentication\ yes/PubkeyAuthentication\ yes/' /etc/ssh/sshd_config && \
    sed -i 's/#RSAAuthentication\ yes/RSAAuthentication\ yes/' /etc/ssh/sshd_config && \
    service sshd restart && \
    chmod 600 /home/vagrant/.ssh/authorized_keys
  "

  config.omnibus.chef_version = "11.6.2"
  config.vm.provision :chef_solo do |chef|
    chef.json = {
      'zabbix' => {
        'agent' => {
          'servers' => [server_ip],
          'servers_active' => [server_ip]
        },
      }
    }
    chef.add_recipe "zabbix-agent"
  end 
end
