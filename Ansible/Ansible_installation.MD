# Ansible Installation

Ansible is an open source automation platform. It is very, very simple to setup and yet powerful. Ansible can help you with configuration management, application deployment, task automation.



### Prerequisites

1. An AWS EC2 instance 

### Installation steps:

Add a EPEL (Extra Packages for Enterprise Linux)third party repository to get packages for Ansible 
```sh 
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

Install Ansible
```sh 
yum install ansible -y 
```

Check Ansible version 

```sh 
ansible --version
```

Create a new user for ansible administration & grant admin access to user (Master and Slave)
```sh 
useradd ansadmin
passwd ansadmin
# below command addes ansadmin to sudoers file. But strongly recommended to use "visudo" command if you are aware vi or nano editor. 
echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers


```

Using keybased authentication is advised. If you are still at learning stage use password based authentication (Master & Slave)
Setting up Password Authentication
EC2(Amazon Linux Server), default password authentication setting is no

$ vi /etc/ssh/sshd_config
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes
#PermitEmptyPasswords no
#PasswordAuthentication no

``` 
Then restart the service:

service sshd restart

Login as a ansadmin user on master and generate ssh key (Master)
```sh 
ssh-keygen
```
Copy keys onto all ansible client nodes (Master)
```sh 
ssh-copy-id <target-server>
```

Update target servers information on /etc/ansible/hosts file (Master)
```sh 
echo "<target server IP>" > /etc/ansible/hosts
```
Run ansible command as ansadmin user it should be successful (Master)
```sh 
ansible all -m ping
```
