# MultiVM Vagrant Environment
This repository contains Vagrant configuration for a multi-VM environment with:
- 1 CentOS 7 database server (db01)
- 2 Ubuntu 20.04 web servers (web01, web02)
## Prerequisites
- Vagrant
- VirtualBox
## Network Configuration
- db01: 192.168.56.12
- web02: 192.168.56.11
- web01: 192.168.56.10
## Usage
1. Clone this repository
2. Run `vagrant up` to start all VMs
3. Use `vagrant ssh <vm-name>` to connect to specific VM
4. Use `vagrant destroy` to remove all VMs
## Database Access
- Host: 192.168.56.12
- Database: testdb
- User: dbuser
## Backup
A backup script is installed on db01 at /root/backup_db.sh
