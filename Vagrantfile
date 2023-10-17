# -*- mode: ruby -*-
# vi: set ft=ruby :

# Variables 

# Note that i only tested this in ubuntu/bionic64
$box_image = "ubuntu/bionic64"
$user = "vagrant"

$install_docker = <<SCRIPT
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
SCRIPT

$config_docker = <<SCRIPT
sudo useradd -m #{$user} -s /bin/bash 
sudo groupadd docker
sudo usermod -aG docker #{$user}
newgrp docker
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
SCRIPT

#DOCKER SETUP
$install_jenkins = <<SCRIPT
sudo docker network create jenkins

sudo docker run \
  --name jenkins-docker \
  --rm \
  --detach \
  --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind \
  --storage-driver overlay2
SCRIPT


# NO DOCKER SETUP

# $install_jenkins = <<SCRIPT
# sudo apt update
# sudo apt install openjdk-11-jdk -y
# sudo apt install maven -y
# curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
# /usr/share/keyrings/jenkins-keyring.asc > /dev/null
  
# echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
# https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
# /etc/apt/sources.list.d/jenkins.list > /dev/null
 
# sudo apt-get update
# sudo apt-get install fontconfig openjdk-11-jre
# sudo apt-get install jenkins
# SCRIPT

$launch_monitoring = <<SCRIPT
  git clone https://github.com/cristianpb/telegraf-influxdb-grafana-docker.git
  cd telegraf-influxdb-grafana-docker
  sudo docker compose up
SCRIPT

Vagrant.configure("2") do |config|

    config.vm.box = $box_image
    config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
    config.vm.network "forwarded_port", :guest => 8080, :host => 8080
    config.vm.network "forwarded_port", :guest => 8083, :host => 8083
    config.vm.network "forwarded_port", :guest => 8086, :host => 8086
    config.vm.network "forwarded_port", :guest => 8090, :host => 8090
    config.vm.network "forwarded_port", :guest => 8099, :host => 8099
    config.vm.network "forwarded_port", :guest => 3000, :host => 3000
    config.vm.network "forwarded_port", :guest => 8888, :host => 8888
    
    config.vm.provision "shell", inline: <<-SHELL
        #{$install_docker}
        #{$install_jenkins}
        #{$config_docker}
        #{$launch_monitoring}
    SHELL
end    