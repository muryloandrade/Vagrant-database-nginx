$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  
  config.vm.define "mysqldb" do |mysql|
  
  mysql.vm.network "public_network", ip:"172.20.0.57", bridge: "wlp0s20f3"
  
  mysql.vm.provision "shell", 
  inline:"cat /configs/id.public >> .ssh/authorized_keys"
  mysql.vm.provision "shell", 
  inline: $script_mysql
  mysql.vm.provision "shell", 
  inline:"cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
  mysql.vm.provision "shell", 
  inline:"service mysql restart"
  
  mysql.vm.synced_folder "./configs", "/configs"
  mysql.vm.synced_folder ".", "/vagrant", disabled: true
end


config.vm.define "phpweb" do |phpweb|

  phpweb.vm.network "forwarded_port", guest:8097 , host:8097
  phpweb.vm.network "public_network", ip:"172.20.0.58", bridge:"wlp0s20f3"
  
  phpweb.vm.provision "shell",
    inline:"apt-get update && apt-get install -y puppet"
  
  phpweb.vm.provision "puppet" do |puppet|
    puppet.manifests_path = "./configs/manifest"
    puppet.manifest_file = "phpweb.pp"

  end
end
end