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

ahora colamos el repositorio 

```bash  
 sudo git clone https://github.com/cloudacademy/static-website-example /var/www/servidor/html

 ```