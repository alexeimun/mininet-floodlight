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
        echo "Step 1). Running sudo apt-get update..." && apt-get -qq -y update
        echo "Step 2). Getting openjsdk-7-jdk and other java tools..." && apt-get -qq -y install openjdk-7-jdk ant maven python-dev eclipse
        echo "Step 3). Installing other neccessary tools..." && apt-get -qq -y install build-essential powerstat unzip git
        gem install -q tmuxinator
    SHELL
    
    config.vm.provision "shell", privileged: false, inline: <<-SHELL
        echo "Step 4). Setting up the environment..."
        mkdir ~/.tmuxinator
        echo 'alias fl="cd ~/floodlight-1.2 && ant && java -jar target/floodlight.jar"' >> ~/.bashrc
        echo "export EDITOR='vim'" >> ~/.bashrc
        echo 'LC_ALL="en_US.UTF-8"' | sudo tee /etc/default/locale
        source /etc/default/locale
        echo JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64 >> ~/.bashrc
        echo "tmuxinator start main" >> ~/.bashrc
        cd ~/
        echo "Step 5). Downloading mininet please wait..." && git clone --quiet git://github.com/mininet/mininet
        cd mininet
        git tag && git checkout -b 2.2.1 2.2.1
        chmod +x ./util/install.sh && ./util/install.sh -a
        cd ~/ && echo "Downloading floodlight please wait..." && wget -q https://codeload.github.com/floodlight/floodlight/zip/v1.2 -O pfl.zip
        unzip -qq pfl.zip
        echo "Step 6). Finishing installation..."
        echo "Step 7). Run vagrant ssh command to enter to the machine!"
        echo "Enjoy!"
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
