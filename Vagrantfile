
Vagrant.configure("2") do |config|

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

