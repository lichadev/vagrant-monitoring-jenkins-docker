# -*- mode: ruby -*-
# vi: set ft=ruby :
$box_image = "ubuntu/bionic64"
$install_docker = <<SCRIPT
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg -y
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
SCRIPT
$launch_monitoring = <<SCRIPT
  git clone https://github.com/cristianpb/telegraf-influxdb-grafana-docker.git
  cd telegraf-influxdb-grafana-docker
  make up
SCRIPT

Vagrant.configure("2") do |config|

    config.vm.box = $box_image
    config.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end
    config.vm.network "forwarded_port", :guest => 8083, :host => 8083
    config.vm.network "forwarded_port", :guest => 8086, :host => 8086
    config.vm.network "forwarded_port", :guest => 8090, :host => 8090
    config.vm.network "forwarded_port", :guest => 8099, :host => 8099
    config.vm.network "forwarded_port", :guest => 3000, :host => 3000
    config.vm.network "forwarded_port", :guest => 8888, :host => 8888
    
    config.vm.provision "shell", inline: <<-SHELL
        #{$install_docker}
        #{$launch_monitoring}
    SHELL
end    