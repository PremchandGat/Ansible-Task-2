# Ansible-Task-2

Launch instance on AWS cloud

[root@localhost ~]# cd /
[root@localhost ~]# mkdir myroles
[root@localhost /]# cd  myroles/
[root@localhost myroles]# ansible-galaxy init launch-instance
- Role launch-instance was created successfully
[root@localhost myroles]# cd launch-instance/
[root@localhost launch-instance]# ls
defaults  files  handlers  meta  README.md  tasks  templates  tests  vars
[root@localhost launch-instance]# cd tasks
[root@localhost tasks]# vim main.yml
---

# tasks file for launch-instance                        
- name: Launching the Webservers EC2 instances on AWS
  ec2:
    region: "ap-south-1"
    image: "ami-052c08d70def0ac62"
    instance_type: "t2.micro"
    count: 1
    vpc_subnet_id: "{{ subnet }}"
    group_id: "{{ sg }}"
    instance_tags:
      Name: "webserver"
    key_name: "{{ key }}"
    assign_public_ip: yes
    state: present
    wait: yes
    aws_access_key: "{{ access }}"
    aws_secret_key: "{{ secret }}"
  register: webserver_logs
- debug:
     var: webserver_logs

[root@localhost tasks]# cd ../vars/
[root@localhost vars]# vim main.yml 
---
# vars file for launch-instance
secret: "Write your secret key"
access: "Write your access key"
key: "mykey"
subnet: "subnet-7eccc816"                               
sg: "sg-078531d324434dcae"



[root@localhost /]# cd myroles/
[root@localhost myroles]# ansible-galaxy init conf-webserver
- Role conf-webserver was created successfully
[root@localhost myroles]# cd conf-webserver/
[root@localhost conf-webserver]# ls
defaults  files  handlers  meta  README.md  tasks  templates  tests  vars
[root@localhost conf-webserver]# cd tasks/
[root@localhost tasks]# vim main.yml
---
# tasks file for conf-webserver                                                                       
- name: Installing Apache Web Server (httpd)
  package:
    name: httpd
    state: present

- name: Copying webpage
  copy:
    content: "<h1>Configured by Ansible</h1>\n <h2>Premchand</h2>{{ ansible_hostname }}"                  
    dest: "/var/www/html/index.html"
    mode: 644

- name: Enabling and starting the web server
  service:
    name: "httpd"
    state: started
    enabled: yes
[root@localhost tasks]# cd /
[root@localhost /]# mkdir ip
[root@localhost /]# cd ip
[root@localhost ip]# wget 
[root@localhost ip]# wget
[root@localhost ip]# chmod +x ec2.ini
[root@localhost ip]# chmod +x ec2.py
[root@localhost ip]# ls
ec2.ini  ec2.py

[root@localhost /]# vim task2.yml
- hosts: localhost
  roles:
          - role:  launch-instance

- hosts: tag_Name_webserver
  roles:
    - role: conf-webserver

[root@localhost /]# vim /etc/ansible/ansible.cfg
[defaults]
inventory=/ip/
roles_path=/myroles
host_key_checking = False
private_key_file = /mykey.pem
remote_user = ec2-user
ask_pass = False
become = TRUE

[privilege_escalation]
become = TRUE
become_user = root
become_method = sudo
become_ask_pass = FALSE


[root@localhost /]# chmod 400 mykey.pem
[root@localhost /]# chmod 600 mykey.pem
[root@localhost /]# ls -l
-rw-------.   1 root root 1674 Jul 30 23:37 mykey.pem

[root@localhost /]# export AWS_REGION=ap-south-1
[root@localhost /]# export AWS_ACCESS_KEY_ID=AKIAYXXXXXXXXXXXXXXX
[root@localhost /]# export AWS_SECRET_ACCESS_KEY=vDDg94XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

