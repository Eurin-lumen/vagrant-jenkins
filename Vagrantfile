Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/23.04"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  config.vm.network "forwarded_port", guest: 8080, host: 8080
  config.vm.network "forwarded_port", guest: 50000, host: 50000

  config.vm.provision "shell", inline: <<-SHELL
    # Mise à jour du système
    sudo apt-get update
    sudo apt-get upgrade -y

    # Installer les dépendances
    sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common

    # Ajouter le référentiel Docker
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    # Installer Docker
    sudo apt-get update
    sudo apt-get install -y docker-ce

    # Ajouter l'utilisateur vagrant au groupe docker
    sudo usermod -aG docker vagrant

    # Installer Docker Compose
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose

    # Créer un fichier docker-compose.yml pour Jenkins
    mkdir /home/vagrant/jenkins
    cat << EOF > /home/vagrant/jenkins/docker-compose.yml
    version: '3.8'
    services:
      jenkins:
        image: jenkins/jenkins:lts
        container_name: jenkins
        ports:
          - "8080:8080"
          - "50000:50000"
        volumes:
          - jenkins_home:/var/jenkins_home
    volumes:
      jenkins_home:
    EOF

    # Démarrer Jenkins
    cd /home/vagrant/jenkins
    sudo docker-compose up -d
  SHELL
end
