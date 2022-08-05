
$script_mysql = <<-SCRIPT
    apt-get update && \
    apt-get install -y mysql-server-5.7 && \
    mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

$script_key = <<-SCRIPT
    cat /configs/key.pub >> .ssh/authorized_keys
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  config.vm.define "mysqldb" do |mysql|

    mysql.vm.provision "shell",
                        inline: $script_key

    mysql.vm.provision "shell",
                        inline: $script_mysql
    mysql.vm.provision "shell",
                        inline: "cat /configs/mysql.cnf > /etc/mysql/mysql.conf.d/mysql.conf"
    mysql.vm.provision "shell",
                        inline: "service restart mysql"

    mysql.vm.synced_folder "./configs", "/configs"
    mysql.vm.synced_folder ".", "/vagrant",
                        disabled: true

  end

  config.vm.define "phpweb" do |phpweb|
  phpweb.vm.network "forwarded_port", guest: 8888, host: 8888
  phpweb.vm.network "public_network"

  phpweb.vm.provision "shell" ,
                       inline: "apt-get update && apt get install -y puppet"

    phpweb.vm.provision "puppet" do |puppet|
    puppet.manifests_path = "./configs/manifests"
    puppet.manifest_file = "phpweb.pp"

    end
  end
end
