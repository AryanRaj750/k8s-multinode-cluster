<!--- Headings --->
>>># **K8s Multinode Cluster**

<br>

>## **Prerequisities** 
>- python script file for retrieving IPs of running instances e.g *[ec2.py](https://raw.githubusercontent.com/ansible/ansible/stable-1.9/plugins/inventory/ec2.py)*
>- python configuration file e.g *[ec2.ini](https://raw.githubusercontent.com/ansible/ansible/stable-1.9/plugins/inventory/ec2.ini)*
>- Ansible version 2.7
>- python 3.6 or above 
>- boto3 python module

<br>

> ## **AWS Configure** 
>### Run these commands configure AWS:-
>**you must have IAM user**
>- export AWS_ACCESS_KEY_ID='YOUR_AWS_API_KEY'
>- export AWS_SECRET_ACCESS_KEY='YOUR_AWS_API_SECRET_KEY'
>- export ANSIBLE_HOSTS=/etc/ansible/ec2.py
>- export EC2_INI_PATH=/etc/ansible/ec2.ini
>- ssh-add ~/.ssh/keypair.pem // first copy pem file in /root/.ssh/ directory then run
>
> [Follow this for better understanding](https://docs.ansible.com/ansible/2.5/user_guide/intro_dynamic_inventory.html#example-aws-ec2-external-inventory-script)

<br>

> ## **Test** 
>Both **[ec2.py](https://raw.githubusercontent.com/ansible/ansible/stable-1.9/plugins/inventory/ec2.py)** and **[ec2.ini](https://raw.githubusercontent.com/ansible/ansible/stable-1.9/plugins/inventory/ec2.ini)** files should be present in /etc/ansible/ direcory. and the [ec2.py](https://raw.githubusercontent.com/ansible/ansible/stable-1.9/plugins/inventory/ec2.py) file should be executable (run chmod +x [ec2.py](https://raw.githubusercontent.com/ansible/ansible/stable-1.9/plugins/inventory/ec2.py) to make executable)/
>* Run ./ec2.py --list

<br>

>## **Ansible Role**
>This repo contains three roles
>1. ec2Instance_launch 
>2. k8s_master
>3. k8s_worker
>### **ec2Instance_launch**
>- ec2Instance_launch role have for launching instances on AWS cloud. ```By default this role will three launch by tagging first instance as master. Second instance as worker node and third instance as workernode also```.  Inside vars folder/directory you will get a variable named as **tags** which is of list types. inside this variable i have passed tags name of 
each instance. So that, it can be easy to identify worker node by tag name. According to your choice you can pass tags name inside that list. But remember one thing that how many 
tags name you will pass in tags variable that number of instances will be launched.
>### **k8s_master role**:
>- This role will help to comfigure instance tagged with master as kubernetes master node. But if you want a specific tag for your master node then you can tagged but inside this
role in tasks directory you have to update hosts name as your tagged name. so, that this role can understand that which instance you want to configure as master node
example:- in my case hosts: tag_node_master but in your case if you changed then it would be tag_key_value as hosts: **tag_env_prod** e.g **key=env, value=prod**.
`There are two files inside files directory of role e.g daemon.json, kubernetes,repo`.  **daemon.json file is used to changed the driver of runtime**. ```By default driver is
cgroupfs but k8s support systemd driver for runtime```. for confirmation you can run "docker info | grep csgroupfs". so, you have to change cgroupfs driver as systemd. for this 
i have used daemon.json file so you have to need change it manually. The second file is kubernetes.repo, this file is used for reconfigure yum repository. so that we can install 
kubeadm , kubelet, kubectl package. 
In tasks main.yml, master node is initialize/configure with **```cidr=10.244.0.0/16```** because flannel pods allow network of **```10.244.0.0/16```**. But if you want to change cidr then you can
change but you have to update configmap file of flannel pod maually. otherwise, **```coreDns```** would not work. it would be in running state but not gives service of kube-proxy.
>### **k8s_worker role**:
>- This role would help to configure instances as workernode for k8s master. ```In this role, inside files directory there would be three file daemon.json, kubernetes.repo and k8s.conf```.
*daemon.json and kubernetes.repo file are same whatever are in k8s_master role*. But k8s.conf file is different file. k8s.conf file is used for some changing in kernel setting for 
bridge network connection. k8s.conf will enables bridge for connection. ```workernode will be join automatically to master node```. you don't need to join it.

>## Inventory File
> *Inside your inventory these keywords should be present* 
<br>
Define `become_user=" ", remote_user=" "` keyword either in inventory file or in playbook or at both places.
>![inventory look likes](https://drive.google.com/uc?export=view&id=1ojDzvqniPG4TvCyETKxGMJFD4TmmCrC1)

<br>

>## Run playbook
> To run playbook write command
><br> ansible-playbook < file name >

<br>

>## Help
>For any query you can mail me <br>
Email:- ar9131000@gmail.com <br>
LinkedIn:- https://www.linkedin.com/in/aryanraj750/
