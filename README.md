# Nginx
    Primero creamos la maquina virtual con este codigo:

  ```bash Vagrant.configure("2") do |config|

  config.vm.define "nginx" do |nginx|
      nginx.vm.box = "debian/bookworm64"  
      nginx.vm.hostname = "nginx.sistema.test"
      nginx.vm.network "private_network", ip: "192.168.57.102"  
      nginx.vm.provider "virtualbox" do |vb|
          vb.memory = "512"  
          vb.cpus = 1  
      end
  end

end
```

Ahora en la maquina virtual descargamos nginx y comprobamos su estatus:

```bash  
 sudo apt update
 sudo apt install nginx
 systemctl status nginx
 ```


2. Creación de las carpeta del sitio web
Primero descargamos git en la maquina virtual
```bash  
 sudo apt install git
 ```

ahora clonamos el repositorio 

```bash  
 sudo git clone https://github.com/cloudacademy/static-website-example /var/www/servidor/html

 ```

  Además, haremos que el propietario de esta carpeta y todo lo que haya dentro sea el usuario www
data, típicamente el usuario del servicio web.

```bash  
 sudo chown -R www-data:www-data /var/www/servidor/html
```

Y le daremos los permisos adecuados para que no nos de un error de acceso no autorizado al entrar
 en el sitio web:

 ```bash  
 sudo chmod -R 755 /var/www/servidor
```

3. Configuración de servidor web NGINX

crear archivo de configuracion:
 
 ```bash  
 sudo nano /etc/nginx/sites-available/servidor
```

agregamos esto al archivo:
 server {
         listen 80;
         listen [::]:80;
         root /var/www/servidor/html;
         index index.html index.htm 
         server_name servidor;
         location / {
                try_files $uri $uri/ =404;
        }
 }

 crear archivo simbolico:
 sudo ln -s /etc/nginx/sites-available/servidor /etc/nginx/sites-enabled/

 ahora reiniciamos nginx:

 sudo systemctl restart nginx

 instalacion vsftpd
sudo apt-get install vsftpd

crear directorio ftp
mkdir /home/vagrant/ftp

generar certificados
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt

   eliminar y agregar las lineas ssl 
      sudo nano /etc/vsftpd.conf


   rsa_cert_file=/etc/ssl/certs/vsftpd.crt
   rsa_private_key_file=/etc/ssl/private/vsftpd.key
   ssl_enable=YES
   allow_anon_ssl=NO
   force_local_data_ssl=YES
   force_local_logins_ssl=YES
   ssl_tlsv1=YES
   ssl_sslv2=NO
   ssl_sslv3=NO
   require_ssl_reuse=NO
   ssl_ciphers=HIGH
   
   local_root=/home/vagrant/ftp

   reiniciar vsftpd

      sudo systemctl restart vsftpd