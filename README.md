# ANSIBLE PLAYBOOKS on AWS ARCHITECTURE

This project creates webserver and a MySQL database using Ansible-playbooks over AWS EC2 instances


## FLOW CHART
#### STAGE 1 - LAUNCHING AWS EC2 INSTANCES
#### STAGE 2 - GLOBAL CONFIGURATIONS
#### STAGE 3 - FIRST PLAYBOOK - Installs webserver and MySQL service
#### STAGE 4 - SECOND PLAYBOOK - Remote operations on DB
#### STAGE 5 - LOCAL CONFIGURATIONS FOR TEAMWORK

## STEP BY STEP

### STAGE 1 - LAUNCHING AWS EC2 INSTANCES
#### We create 4 instances - one for our Ansible control machine through which we will work, 2 web servers to observe differences and one db
Login to your AWS account and launch EC2 instances as follows:
1. Control Machine - Ubuntu t2.micro [Control Machine SG: 22/MyIP] for Ansible with user data script from here - create new key-pair: Control-key.pem 
2. Web01/Web02 - Two instances, centOS7 (free tier) [WebServer SG: 22/MyIP -- 22/Control Machine SG -- 80/MyIP] create new key-pair vprofile-key.pem (( A better practice would be to use port 443 and attach the apropriate ACM rather than using port 80, but for the ease of use I'm using port 80 ))
4. db01 - centOS7 (free tier) [DBServer SG: 22/MyIP -- 22/Control Machine SG] use key-pair vprofile-key.pem

### STAGE 2 - GLOBAL CONFIGURATIONS
#### Create inventory file, general configuration file and dependencies 
* SSH connect to Control machine and create vprofile/exercise directory and cd into it and create the following files:
* copy inventory file content --> vim inventory --> paste content --> replace <Private IP> with Private IP of instances accordingly :wq
* copy the content of vprofile-key.pem --> vim vprofile-key.pem --> paste content :wq --> chmod 400 vprofile-key.pem
* sudo vim /etc/ansible/ansible.cfg --> look for line # host_key_checking=False and activate it (un-#)
^ There might be a prompt message to create a config file instead of the file itself, so we might need to >
* cd /etc/ansible
* mv ansible.cfg ansible.cfg_backup
* run command (sudo) ansible-config init --disabled > ansible.cfg
^ and only then we can make the change > look for line # host_key_checking=False and activate it (un-#) :wq
* return to working directory

### STAGE 3 - FIRST PLAYBOOK - web_db.yaml
#### Installs webserver and MySQL service 
> This file contains tasks that takes place on both web01 and db01 servers
>
> On web01 - it installs, starts and enables apache httpd and -- deployment of web file (index.html)
>
> On db01 - it installs, starts and enables mariadb (MySQL server)
* vim web_db.yaml and copy the content of the file from web_db.yaml file in this library. :wq
* vim index.html and copy the content of the file from index.html file in this library. :wq
* run command ansible-palybook -i inventory db_web.yaml to run this playbook
* We can check on our browser - connecting <web01 public IP>:80 - to see if we get the text in file index.html

### STAGE 4 - SECOND PLAYBOOK
#### Enables remote operations on DB
> This playbook works only on db01 server -
> 
> It install the module that connects python and mysql service
>
> It installs, starts and enables mariadb service
>
> It Creates a new 'accounts' DB, username 'admin' and password '12345'
* vim db.yaml and copy the content of file db.yaml in this library. :wq
* run command ansible-playbook -i inventory db.yaml to run this playbook

### STAGE 5 - LOCAL CONFIGURATIONS FOR TEAMWORK
#### Creating local configuration file and log file with privileges
> Within working directory:
* vim ansible.cfg and copy content of ansible.cfg file in this directory :wq
> We create defaults of:
> 
> inventory file by location
> 
> log file by path (/var/log/ansible.log)
> 
> and we set the privileges for root user to make sudo operations

* We need to create the log file first -->  sudo touch /var/log/ansible.log
* And to give it ownership and user permissions for ubuntu user --> sudo chown ubuntu.ubuntu /var/log/ansible.log
* run command ansible-playbook db.yaml (can add -vv at the end for verbose)

