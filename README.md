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

```bash  
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
```
 

crear archivo simbolico:
 
```bash  
 sudo ln -s /etc/nginx/sites-available/servidor /etc/nginx/sites-enabled/
```
 ahora reiniciamos nginx:
```bash  
 sudo systemctl restart nginx
```
 

4. instalacion vsftpd
```bash  
 sudo apt-get install vsftpd
```

```bash
crear directorio ftp
mkdir /home/vagrant/ftp
```

generar certificados
```bash
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt
```
eliminar y agregar las lineas ssl
```bash 
    sudo nano /etc/vsftpd.conf
```
```bash 
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
```
   reiniciar vsftpd
```bash 
      sudo systemctl restart vsftpd
```


5. Tarea
crear carpeta
```bash  
sudo mkdir -p /var/www/nuevaweb/html
```

 Crear un archivo de configuración en sites-available
Crea un archivo de configuración para tu nuevo dominio en el directorio /etc/nginx/sites-available/

```bash
Copiar código
sudo nano /etc/nginx/sites-available/nuevaweb
```
 Agregar la configuración del servidor
Dentro del archivo, copia y pega la siguiente configuración

```bash
server {
    listen 80;
    listen [::]:80;
    root /var/www/nuevaweb/html;
    index index.html index.htm;

    server_name nuevaweb.com www.nuevaweb.com;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

 Crear un enlace simbólico en sites-enabled

```bash
sudo ln -s /etc/nginx/sites-available/nuevaweb /etc/nginx/sites-enabled/
```
 Reiniciar Nginx para aplicar los cambios
```bash
sudo systemctl restart nginx
```
Modificar el archivo /etc/hosts
Abre el archivo /etc/hosts con un editor de texto:

```bash
sudo nano /etc/hosts
```
Agrega una línea con la IP de la máquina virtual y el dominio:

```bash
192.168.57.102 nuevaweb.com www.nuevaweb.com
```

6. ¿Qué pasa si no hago el link simbólico entre sites-available y sites-enabled de mi sitio web?
Si no creas el enlace simbólico entre sites-available y sites-enabled, Nginx no reconocerá ni cargará la configuración de tu sitio web. Esto sucede porque Nginx solo lee los archivos de configuración dentro del directorio sites-enabled al iniciar o recargar su servicio. Aunque el archivo esté en sites-available, no tendrá efecto hasta que esté vinculado.

¿Qué pasa si no le doy los permisos adecuados a /var/www/nombre_web?
Si no configuras los permisos correctamente en el directorio /var/www/nombre_web, pueden ocurrir problemas como fallos al cargar el sitio web, errores de acceso a recursos internos o inclusio pueden haber problemas de seguridad