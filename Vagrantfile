# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  # config.vm.box = "base"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Disable the default share of the current code directory. Doing this
  # provides improved isolation between the vagrant box and your host
  # by making sure your Vagrantfile isn't accessible to the vagrant box.
  # If you use this you may want to enable additional shared subfolders as
  # shown above.
  # config.vm.synced_folder ".", "/vagrant", disabled: true

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL

  # Web Server
  config.vm.define "web" do |web|
    web.vm.box = "ubuntu/bionic64"
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.56.10"
    web.vm.network "forwarded_port", guest: 80, host: 8080
    web.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install -y nginx
      sudo systemctl enable nginx
      sudo systemctl start nginx

      # sudo apt update && sudo apt install -y nginx php-fpm php-mysql
      # sudo nano /etc/nginx/sites-available/default
      # location ~ \.php$ {
      #   include snippets/fastcgi-php.conf;
      #   fastcgi_pass unix:/run/php/php7.2-fpm.sock;
      #   fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      #   include fastcgi_params;
      # }
      # sudo nginx -t
      # sudo systemctl restart nginx
      # sudo nano /var/www/html/index.php
      # <?php
      # $conn = new mysqli("192.168.56.11", "vagrant", "vagrant", "test_db");
      # if ($conn->connect_error) {
      #    die("Connection failed: " . $conn->connect_error);
      # }

      # $result = $conn->query("SELECT * FROM users");

      # echo "<h1>Users List</h1>";
      # while ($row = $result->fetch_assoc()) {
      #    echo "<p>" . $row["id"] . ". " . $row["name"] . "</p>";
      # }

      # $conn->close();
      # ?>
      # sudo systemctl restart nginx

    SHELL
  end

  # Database Server
  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/bionic64"
    db.vm.hostname = "db"
    db.vm.network "private_network", ip: "192.168.56.11"
    db.vm.network "forwarded_port", guest: 3306, host: 3307
    db.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt install -y mysql-server
      sudo systemctl enable mysql
      sudo systemctl start mysql

      sudo mysql -e "CREATE DATABASE IF NOT EXISTS test_db;"
      sudo mysql -e "CREATE USER IF NOT EXISTS 'vagrant'@'%' IDENTIFIED BY 'vagrant';"
      sudo mysql -e "GRANT ALL PRIVILEGES ON test_db.* TO 'vagrant'@'%' WITH GRANT OPTION;"
      sudo mysql -e "FLUSH PRIVILEGES;"

      sudo mysql -e "USE test_db; CREATE TABLE IF NOT EXISTS users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(100) NOT NULL);"
      sudo mysql -e "USE test_db; INSERT INTO users (name) VALUES ('Alice'), ('Bob'), ('Charlie') ON DUPLICATE KEY UPDATE name=name;"

      sudo sed -i "s/^bind-address.*/bind-address = 0.0.0.0/" /etc/mysql/mysql.conf.d/mysqld.cnf
      sudo systemctl restart mysql
    SHELL
  end

end
