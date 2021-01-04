# AWX installation Steps - Centos 8

- [AWX](#AWX)
   - [Overview](#overview)
   - [Minimum Requirements](#minimum-requirements)
   - [Installation](#installation)
	    - [Installing epel repo and dependencies](installing-epel-repo-and-dependencies)
      - [Installing Docker & Docker-compose](installing-docker-&-docker-compose)
      - [Clone the AWX repository & Installation](clone-the-AWX-repository-&-Installation)

### Overview

**`AWX`** is a free community edition of Ansible tower. It is an upstream project from which ansible tower is derived. It is free and doesn't require any license.

### Minimum Requirements

- Ubuntu 20.04
- At least 4GB of RAM – More is better
- 2vcpus – more if you have
- 10GB free disk storage

### Installation

#### Installing epel repo and dependencies

```
sudo dnf -y install epel-release
sudo dnf -y install dnf-plugins-core
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf config-manager --set-enabled powertools
```

AWX requires some dependency packages lets install them using the below command.

```
sudo dnf install -y git python3-pip curl ansible vim gcc nodejs gcc-c++  gettext lvm2 device-mapper-persistent-data pwgen bzip2
```

Disable SELINUX using below command

```
sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
sudo setenforce 0
```

#### Installing Docker & Docker-compose


AWX can be deployed only as containerized applications. Hence it needs to be deployed only on Docker-compose, kubernetes cluster or openshift cluster. 
Let us docker compose in this scenario.

Lets install docker first

```
sudo curl  https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
sudo yum makecache 
sudo dnf -y  install docker-ce --nobest
systemctl start docker
systemctl enable docker
```

check whether docker service is running or not using below command.

```
systemctl start docker
```
Add the user to docker group so that docker commands can be run with out sudo.

```
usermod -aG docker $(whoami)
```

now lets install docker module & docker-compose using the below commands.

```
sudo pip3 install -U docker docker-compose
```

check whether docker-compose is installed or not using below command.

```
docker-compose version
```

If you are not able to find docker-compose then run the below command.

```
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose version
```

#### Clone the AWX repository & Installation

```
cd ~
git clone --depth 50 https://github.com/ansible/awx.git
cd awx/installer
```

we need to edit the inventory file and set the the admin password(used for logging in). to set the strong password lets generated one using 'pwgen' utility.

```
pwgen -N 1 -s 30
```

**Note:** Above command generates strong random key which we used for admin logging and setting the same in inventory file.

open the inventory file and set "admin_password" to random generated password, "awx_alternate_dns_servers" to "8.8.8.8,8.8.4.4".

Add Firewall rules:

In case you have Firewalld running, add-masquerade and allow http and https services. This is very important, do not skip it. 
In case you changed the port in the previous inventory file, allow it in your firewall as well.

```
sudo firewall-cmd --zone=public --add-masquerade --permanent
sudo firewall-cmd --permanent --add-service={http,https}
sudo firewall-cmd --reload
```

Lets create a directory "/var/lib/awx/projects" same has been mentioned in inventory file that is where all the awx projects gets created.

```
sudo mkdir -p /var/lib/awx/projects
```
execute the playbook

```
ansible-playbook -i inventory install.yml -vv
```
Everything will be successfull and awx is deployed as docker conatainers. check the below command.

docker ps

**Note:** Redis is used for cache, postgres is used for storing the data, nginx is used for frontend ui.

Access the frontend ui at http://\<IPaddress\>:80
