# Vagrantfile

# Use version 2 of Vagrant syntax
Vagrant.configure("2") do |config|

  # Set the box image to CentOS 7
  config.vm.box = "centos/7"

  # Disable checking for box image updates
  config.vm.box_check_update = false

  # Define a virtual machine with the hostname "web01"
  config.vm.define "web01" do |web01|
    
    # Set the hostname of the virtual machine
    web01.vm.hostname = "web01.qa.vegspoit.ag"
    
    # Add a private network interface with a static IP address
    web01.vm.network "private_network", ip: "192.168.56.10"
    
    # Configure shell provisioner to execute commands on the virtual machine
    web01.vm.provision "shell", inline: <<-SHELL      
      # Install vim 
      sudo yum install vim -y
      # Display IP address of the virtual machine
      ip addr show
    SHELL
  end
end
