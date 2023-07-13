Stage 1 - Launch servers
Login to AWS account and launch EC2 instances as follows:
1. Control Machine - Ubuntu t2.micro [SG: 22/MyIP] for Ansible with user data script from here - create new key-pair: Control-key.pem 
2. Web01/Web02 - Two instances with centOS7 (all free tier) [SG: 22/MyIP -- 22/Control Machine SG -- 80/MyIP] create new key-pair vprofile-key.pem
3. db01 - centOS7 (free tier) [SG: 22/MyIP -- 22/Control Machine SG] use key-pair vprofile-key.pem

Stage 2 - Update inventory file, general configuration file and dependencies 
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

Stage 3 - First playbook: web_db.yaml 
> This file contains tasks that takes place on both web01 and db01 servers
> On web01 - it installs, starts and enables apache httpd and -- deployment of web file (index.html)
> On db01 - it installs, starts and enables mariadb (MySQL server)
* vim web_db.yaml and copy the content of the file from web_db.yaml file in this library. :wq
* vim index.html and copy the content of the file from index.html file in this library. :wq
* run command ansible-palybook -i inventory db_web.yaml to run this playbook
* We can check on our browser - connecting <web01 public IP>:80 - to see if we get the text in file index.html

Stage 4 - Enable remote operations on db
* vim db.yaml and copy the content of file db.yaml in this library. :wq
> This playbook works only on db01 server -
> It install the module that connects python and mysql service
> It installs, starts and enables mariadb service
> It Creates a new 'accounts' DB, username 'admin' and password '12345'
* run command ansible-playbook -i inventory db.yaml to run this playbook

Stage 5 - Shared configuration file for commits
> Within working directory:
* vim ansible.cfg and copy content of ansible.cfg file in this directory
> We create defaults of:
- inventory file by location
- log file by path
> and we set the privileges for root user to make sudo operations
:wq
* We need to create the log file first -->  sudo touch /var/log/ansible.log
* And to give it ownership and user permissions for ubuntu user --> sudo chown ubuntu.ubuntu /var/log/ansible.log
* run command ansible-playbook db.yaml (can add -vv at the end for verbose)
> The logs will be kept at /var/log/ansible.log file
