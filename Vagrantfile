# -*- mode: ruby -*-
# vi: set ft=ruby :



#Structure to configure instance, you can modify this for customize vm
boxes = [
    {
        :name => "swarm01",
        :eth => "192.168.50.20",
        :mem => "2048",
        :cpu => "1",
        :box => "centos/7"
    },
    {
        :name => "swarm02",
        :eth => "192.168.50.21",
        :mem => "2048",
        :cpu => "1",
        :box => "centos/7"
    },
    {
        :name => "swarm03",
        :eth => "192.168.50.22",
        :mem => "2048",
        :cpu => "1",
        :box => "centos/7"
    },
    {
        :name => "monit",
        :eth => "192.168.50.23",
        :mem => "2048",
        :cpu => "1",
        :box => "centos/7"

    },  
]



# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|

  #For eatch boxe
  boxes.each_with_index do |opts,index|

    config.vm.box = opts[:box]

    config.vm.define opts[:name] do |config|
      config.vm.hostname = opts[:name]
      
      #Config to add custom ssh key to vmachine
      config.ssh.private_key_path = ["vagrantkey", "~/.vagrant.d/insecure_private_key"]
      config.vm.provision "file", source: "vagrantkey.pub", destination: "~/.ssh/authorized_keys"
      config.ssh.insert_key = false
      
      config.vm.network "private_network", ip: opts[:eth]    

      config.vm.provision "shell", inline: <<-SHELL
       /bin/echo "Disable SELINUX..."
       /bin/sudo /sbin/setenforce 0       
       /bin/sudo /bin/sed -i 's/SELINUX=\(enforcing\|permissive\)/SELINUX=disabled/g' /etc/selinux/config
       /bin/sudo /bin/sed -i  s/BOOTPROTO=none/BOOTPROTO="static"/g /etc/sysconfig/network-scripts/ifcfg-eth1
       /bin/echo "Fix eth1 down on startup..."
       /bin/sudo /bin/chown root:root /etc/sysconfig/network-scripts/ifcfg-eth1
       /bin/sudo /bin/chmod 644 /etc/sysconfig/network-scripts/ifcfg-eth1
      SHELL

      config.vm.provider "virtualbox" do |vb|
        vb.cpus = opts[:cpu] 
        vb.memory = opts[:mem] 
        vb.name = opts[:name]
      end
        
      # Run Ansible playbook after all every vm created
        # Ansible provisioner.
        config.vm.provision "ansible" do |ansible|
          ansible.playbook = "playbook.yml"
          ansible.inventory_path = 'hosts'
          #ansible.ask_become_pass = true
          end 
        end       
      end
    end    

