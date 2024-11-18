# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    # Common VM settings
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
    end
  
    # Web Server 01
    config.vm.define "web01" do |web01|
      web01.vm.box = "ubuntu/focal64"  # Ubuntu 20.04
      web01.vm.hostname = "web01"
      web01.vm.network "private_network", ip: "192.168.56.10"
      
      web01.vm.provider "virtualbox" do |vb|
        vb.name = "web01"
      end
    end
  
    # Web Server 02
    config.vm.define "web02" do |web02|
      web02.vm.box = "ubuntu/focal64"  # Ubuntu 20.04
      web02.vm.hostname = "web02"
      web02.vm.network "private_network", ip: "192.168.56.11"
      
      web02.vm.provider "virtualbox" do |vb|
        vb.name = "web02"
      end
    end
  
    # Database Server
    config.vm.define "db01" do |db01|
      db01.vm.box = "centos/7"
      db01.vm.hostname = "db01"
      db01.vm.network "private_network", ip: "192.168.56.12"
      
      db01.vm.provider "virtualbox" do |vb|
        vb.name = "db01"
        vb.memory = "2048"  # Increased memory for database server
      end
  
      # DB Server Provisioning
      db01.vm.provision "shell", inline: <<-SHELL
        # Fix CentOS repositories
        sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
        sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
        
        # Clean and update yum cache
        yum clean all
        yum makecache
        
        # Update system & packages
        yum update -y
        yum install -y wget unzip 

        # Install EPEL repository
        yum install -y epel-release
        
        # Install MariaDB and other utilities
        yum install -y mariadb-server mariadb vim wget net-tools
  
        # Start and enable MariaDB service
        systemctl start mariadb
        systemctl enable mariadb
  
        # Secure MySQL installation
        mysql_secure_installation <<EOF

y
Password1234!
Password1234!
y
y
y
y
EOF
  
        # Create database and user
        mysql -u root -pPassword1234! <<EOF
CREATE DATABASE testdb;
CREATE USER 'dbuser'@'%' IDENTIFIED BY 'Password1234!!';
GRANT ALL PRIVILEGES ON testdb.* TO 'dbuser'@'%';
FLUSH PRIVILEGES;
EOF
  
        # Configure MariaDB to allow remote connections
        cp /etc/my.cnf.d/server.cnf /etc/my.cnf.d/server.cnf.bak
        echo "[mysqld]
bind-address = 0.0.0.0" >> /etc/my.cnf.d/server.cnf
  
        # Restart MariaDB to apply changes
        systemctl restart mariadb
  
        # Configure firewall
        systemctl start firewalld
        systemctl enable firewalld
        firewall-cmd --permanent --add-service=mysql
        firewall-cmd --permanent --add-port=3306/tcp
        firewall-cmd --reload
  
        # Install some common database tools
        yum install -y mysqladmin mysql-utilities
        
        # Create a simple backup script
        echo '#!/bin/bash
backup_dir="/var/backup/mysql"
mkdir -p $backup_dir
timestamp=$(date +%Y%m%d_%H%M%S)
mysqldump -u root -pPassword1234! --all-databases > $backup_dir/backup_$timestamp.sql' > /root/backup_db.sh
        
        chmod +x /root/backup_db.sh
  
        echo "Database server setup completed!"
      SHELL
    end
end