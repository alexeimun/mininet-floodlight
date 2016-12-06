# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/trusty64"
    config.vm.network "private_network", type: "dhcp"
    config.vm.network "forwarded_port", guest: 8080, host: 8080
    config.vm.hostname = "mininet"
    config.vm.synced_folder ".", "/vagrant", :mount_options => ["dmode=777", "fmode=666"]
    config.ssh.forward_x11 = true
    ######################## START PROVISION ######################
    config.vm.provision :shell, inline: <<-SHELL
        apt-add-repository ppa:colin-king/powermanagement
        apt-get -y update
        sudo apt-get -y install build-essential openjdk-7-jdk ant maven python-dev eclipse powerstat unzip git
        gem install tmuxinator
    SHELL
    
    config.vm.provision "shell", privileged: false, inline: <<-SHELL
        mkdir ~/.tmuxinator
        echo 'alias fl="cd ~/floodlight-1.2 && ant && java -jar target/floodlight.jar"' >> ~/.bashrc
        echo "export EDITOR='vim'" >> ~/.bashrc
        echo 'LC_ALL="en_US.UTF-8"' | sudo tee /etc/default/locale
        source /etc/default/locale
        echo JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64 >> ~/.bashrc
        echo "tmuxinator start main" >> ~/.bashrc
        cd ~/
        git clone git://github.com/mininet/mininet
        cd mininet
        git tag
        git checkout -b 2.2.1 2.2.1
        ./util/install.sh -a
        cd ~/
        wget https://codeload.github.com/floodlight/floodlight/zip/v1.2 -O pfl.zip
        unzip pfl.zip
    SHELL
    config.vm.provision "file", source: "main.yml", destination: ".tmuxinator/main.yml"
    ###################### END PROVISION ####################
    #Machine attributes
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 2
      vb.name="mininet"
    end
end
