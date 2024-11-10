# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  
    # Configuración común para todas las VMs
    config.vm.box = "ubuntu/focal64"
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
  
    # Servidor Web 1 - Apache
    config.vm.define "web1" do |web1|
      web1.vm.hostname = "web1"
      web1.vm.network "private_network", ip: "192.168.33.10"
      web1.vm.synced_folder "./web1", "/var/www/html"
      
      web1.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y apache2
        systemctl enable apache2
        systemctl start apache2
      SHELL
    end
  
    # Servidor Web 2 - Apache
    config.vm.define "web2" do |web2|
      web2.vm.hostname = "web2"
      web2.vm.network "private_network", ip: "192.168.33.11"
      web2.vm.synced_folder "./web2", "/var/www/html"
      
      web2.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y apache2
        systemctl enable apache2
        systemctl start apache2
      SHELL
    end
  
    # Servidor Balanceador - Nginx
    config.vm.define "lb" do |lb|
      lb.vm.hostname = "lb"
      lb.vm.network "private_network", ip: "192.168.33.12"
      
      lb.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y nginx
        
        # Configuración del balanceador de carga
        cat > /etc/nginx/conf.d/loadbalancer.conf <<EOF
  upstream backend {
      server 192.168.33.10;
      server 192.168.33.11;
  }
  
  server {
      listen 80;
      server_name localhost;
  
      location / {
          proxy_pass http://backend;
          proxy_set_header Host \$host;
          proxy_set_header X-Real-IP \$remote_addr;
          proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto \$scheme;
      }
  }
  EOF
        
        # Eliminar el sitio default de nginx
        rm /etc/nginx/sites-enabled/default
        
        # Reiniciar nginx para aplicar la configuración
        systemctl restart nginx
        systemctl enable nginx
      SHELL
    end
  end