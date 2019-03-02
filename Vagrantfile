#### Writtten By: Oryan Omer####
#Declare Environment
GUI               = false # Enable/Disable GUI
RAM               = 2048   # Default memory size in MB

# Network configuration
DOMAIN            = ".xxx"
NETWORK           = "192.168.50."
NETMASK           = "255.255.255.0"
SYNC_FOLDER       = "/opt/elk"
# Default Virtualbox .box
# See: https://wiki.debian.org/Teams/Cloud/VagrantBaseBoxes
BOX               = "generic/ubuntu1804"

HOSTS = {
   "elk-node1" => [NETWORK+"150", RAM, GUI, BOX],
   "elk-node2" => [NETWORK+"151", RAM, GUI, BOX],
   "elk-node3" => [NETWORK+"152", RAM, GUI, BOX],
   "elk-node4" => [NETWORK+"153", RAM, GUI, BOX],
   "elk-node5" => [NETWORK+"154", RAM, GUI, BOX],
#   "keystore" => [NETWORK+"105", RAM, GUI, BOX],
}

Vagrant.configure(2) do |config|
#Conf each Vm
  config.hostmanager.enabled = false
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  HOSTS.each do | (name, cfg) |

#Config VM Settings
    ipaddr, ram, gui, box = cfg
     config.vm.define name do |machine|
      machine.vm.box   = box
      machine.vm.guest = :debian
      machine.vm.hostname = name
 
     machine.vm.provider :libvirt do |libvirt|
        libvirt.memory = ram
        libvirt.cpus   = 2
#        libvirt.connect_via_ssh = true
      end
      

#Conf Network
     machine.vm.network :private_network, :ip => ipaddr

     machine.vm.network :public_network,
      :dev => "virbr0",
      :mode => "bridge",
      :type => "bridge"

#sync ELK FOlder
     machine.vm.synced_folder SYNC_FOLDER, "/opt/elk/", create: true
     machine.ssh.forward_agent = true
#     machine.ssh.insert_key = true



#install Ansible on master
     if  name == "elk-node1"
        machine.vm.provision "shell", inline: "sudo apt-get update -y  \
       &&  sudo apt-get install software-properties-common -y \
       && sudo apt-add-repository ppa:ansible/ansible \
       && sudo apt-get -y update \
       && sudo apt-get install ansible -y"
     end

       
        
      
# Install Docker  
     machine.vm.provision "shell", inline: "sudo apt-get update -y"
     machine.vm.provision "shell", inline: "sudo apt-get install python python-pip -y"
     machine.vm.provision "shell", inline: "sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y"
     machine.vm.provision "shell", inline: "sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -"
     machine.vm.provision "shell", inline: 'sudo add-apt-repository  "deb [arch=amd64] https://download.docker.com/linux/ubuntu  xenial  stable"'
     machine.vm.provision "shell", inline: "sudo apt-get update -y"
     machine.vm.provision "shell", inline: "sudo apt-get install docker-ce -y"

#Install Docker-Compose
     machine.vm.provision "shell", inline: 'sudo curl -L "https://github.com/docker/compose/releases/download/1.23.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose \
     && sudo chmod +x /usr/local/bin/docker-compose'
     
  end
 
end
end
