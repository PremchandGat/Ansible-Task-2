# Ansible-Task-2

# Create a Folder for roles
<pre>
[root@localhost ~]# cd /
[root@localhost ~]# mkdir myroles
[root@localhost /]# cd  myroles/
</pre>
# Create Role to launch instance on AWS
<pre>
[root@localhost myroles]# ansible-galaxy init launch-instance
- Role launch-instance was created successfully
[root@localhost myroles]# cd launch-instance/
[root@localhost launch-instance]# ls
defaults  files  handlers  meta  README.md  tasks  templates  tests  vars
[root@localhost launch-instance]# cd tasks
[root@localhost tasks]# vim main.yml
</pre>
This is task main file of launch-instance Role
<pre>
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
</pre>
<pre>
[root@localhost tasks]# cd ../vars/
[root@localhost vars]# vim main.yml 
</pre>
This is  vars main file of launch-instance Role
<pre>
---
# vars file for launch-instance
secret: "Write your secret key"
access: "Write your access key"
key: "mykey"
subnet: "subnet-7eccc816"                               
sg: "sg-078531d324434dcae"
</pre>
# create a Role for configuring Httpd webserver 
<pre>
[root@localhost /]# cd myroles/
[root@localhost myroles]# ansible-galaxy init conf-webserver
- Role conf-webserver was created successfully
[root@localhost myroles]# cd conf-webserver/
[root@localhost conf-webserver]# ls
defaults  files  handlers  meta  README.md  tasks  templates  tests  vars
[root@localhost conf-webserver]# cd tasks/
[root@localhost tasks]# vim main.yml
</pre>
This is tasks main file for conf-webserver Role
<pre>
---
# tasks file for conf-webserver                                                                       
- name: Installing Apache Web Server (httpd)
  package:
    name: httpd
    state: present

- name: Copying webpage
  copy:
    content: "Configured by Ansible  Premchand {{ ansible_hostname }}"                  
    dest: "/var/www/html/index.html"
    mode: 644

- name: Enabling and starting the web server
  service:
    name: "httpd"
    state: started
    enabled: yes
</pre>
# Create a inventory folder 
<pre>
[root@localhost tasks]# cd /
[root@localhost /]# mkdir ip
[root@localhost /]# cd ip
[root@localhost ip]# wget https://raw.githubusercontent.com/Premchandg278/Ansible-Task-2/main/ip/ec2.ini
[root@localhost ip]# wget https://raw.githubusercontent.com/Premchandg278/Ansible-Task-2/main/ip/ec2.py
[root@localhost ip]# chmod +x ec2.ini
[root@localhost ip]# chmod +x ec2.py
[root@localhost ip]# ls
ec2.ini  ec2.py
</pre>
# Create a ansible playbook for taask-2
<pre>
[root@localhost /]# vim task2.yml
</pre>
This is task2.yml file
<pre>
- hosts: localhost
  roles:
          - role:  launch-instance

- hosts: tag_Name_webserver
  roles:
    - role: conf-webserver
</pre>
# Configure Ansible 
<pre>
[root@localhost /]# vim /etc/ansible/ansible.cfg
</pre>
ansible.cfg file
<pre>
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
</pre>

# Download your ssh key of aws and give some permissions
<pre>
[root@localhost /]# chmod 400 mykey.pem
[root@localhost /]# chmod 600 mykey.pem
[root@localhost /]# ls -l
-rw-------.   1 root root 1674 Jul 30 23:37 mykey.pem
</pre>
# Declare AWS access key , secret key and region
<pre>
[root@localhost /]# export AWS_REGION=ap-south-1
[root@localhost /]# export AWS_ACCESS_KEY_ID=AKIAYXXXXXXXXXXXXXXX
[root@localhost /]# export AWS_SECRET_ACCESS_KEY=vDDg94XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
</pre>

# Finally run this command
<b>[root@localhost /]# ansible-playbook task2.yml</b>
