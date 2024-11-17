
Vagrant.configure("2") do |config|
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "debian/bookworm64"
    nginx.vm.hostname = "nginx.sistema.test"
    nginx.vm.network "private_network", ip: "192.168.57.102"

   
    nginx.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end

    
    nginx.vm.provision "shell", inline: <<-SHELL
     
      sudo apt update
      sudo apt install -y nginx git vsftpd openssl

      
      sudo mkdir -p /var/www/servidor/html
      sudo git clone https://github.com/cloudacademy/static-website-example /var/www/servidor/html
      sudo chown -R www-data:www-data /var/www/servidor/html
      sudo chmod -R 755 /var/www/servidor

      echo 'server {
          listen 80;
          listen [::]:80;
          root /var/www/servidor/html;
          index index.html index.htm;
          server_name servidor;
          location / {
              try_files $uri $uri/ =404;
          }
      }' | sudo tee /etc/nginx/sites-available/servidor

      sudo ln -s /etc/nginx/sites-available/servidor /etc/nginx/sites-enabled/
      sudo systemctl restart nginx

      
      sudo mkdir -p /var/www/nuevaweb/html
      echo '<!DOCTYPE html><html><body><h1>Nueva Web</h1></body></html>' | sudo tee /var/www/nuevaweb/html/index.html

      echo 'server {
          listen 80;
          listen [::]:80;
          root /var/www/nuevaweb/html;
          index index.html index.htm;
          server_name nuevaweb.com www.nuevaweb.com;
          location / {
              try_files $uri $uri/ =404;
          }
      }' | sudo tee /etc/nginx/sites-available/nuevaweb

      sudo ln -s /etc/nginx/sites-available/nuevaweb /etc/nginx/sites-enabled/
      sudo systemctl restart nginx

      
      sudo mkdir -p /home/vagrant/ftp
      sudo chown -R vagrant:vagrant /home/vagrant/ftp

      sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout /etc/ssl/private/vsftpd.key \
        -out /etc/ssl/certs/vsftpd.crt \
        -subj "/C=US/ST=Denial/L=Springfield/O=Dis/CN=www.example.com"

      sudo sed -i '/^rsa_cert_file/c\\rsa_cert_file=/etc/ssl/certs/vsftpd.crt' /etc/vsftpd.conf
      sudo sed -i '/^rsa_private_key_file/c\\rsa_private_key_file=/etc/ssl/private/vsftpd.key' /etc/vsftpd.conf
      sudo sed -i '/^#ssl_enable/c\\ssl_enable=YES' /etc/vsftpd.conf
      echo "local_root=/home/vagrant/ftp" | sudo tee -a /etc/vsftpd.conf

      sudo systemctl restart vsftpd

      echo "192.168.57.102 nuevaweb.com www.nuevaweb.com" | sudo tee -a /etc/hosts
    SHELL
  end
end

