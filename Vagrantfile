Vagrant.configure("2") do |config|

    # Configuración para el servidor web 1 con Apache
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/jammy64"
      web1.vm.hostname = "web1"
      web1.vm.network "private_network", ip: "192.168.56.11"
      web1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
      SHELL
      # Directorio compartido con la máquina local
      web1.vm.synced_folder "Web1/src", "/var/www/html"
    end
  
    # Configuración para el servidor web 2 con Apache
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/jammy64"
      web2.vm.hostname = "web2"
      web2.vm.network "private_network", ip: "192.168.56.12"
      web2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
      SHELL
      # Directorio compartido con la máquina local
      web2.vm.synced_folder "Web2/src", "/var/www/html"
    end
  
    # Configuración para el servidor de balanceo de carga con Nginx
    config.vm.define "loadbalancer" do |loadbalancer|
      loadbalancer.vm.box = "ubuntu/jammy64"
      loadbalancer.vm.hostname = "loadbalancer"
      loadbalancer.vm.network "private_network", ip: "192.168.56.13"
      loadbalancer.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      loadbalancer.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y nginx
        # Configuración de Nginx para balanceo de carga
        sudo tee /etc/nginx/sites-available/load_balancer > /dev/null <<EOF
        upstream web_servers {
            server 192.168.56.11;
            server 192.168.56.12;
        }
  
        server {
            listen 80;
  
            location / {
                proxy_pass http://web_servers;
            }
        }
        EOF
  
        sudo ln -s /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
        sudo rm /etc/nginx/sites-enabled/default
        sudo systemctl restart nginx
      SHELL
    end
  end
  