# AWX installation steps Ubuntu 20.04.1

- [AWX](#AWX)
   - [Overview](#overview)
   - [Minimum Requirements](#minimum-requirements)
   - [Installation](#installation)
	    - [Update the system](update-the-system)
      - [Installing ansible](installing-ansible)
      - [Installing docker](installing-docker)
      - [Installing docker-compose](installing-docker-compose)
      - [Install nodejs & npm](install-nodejs-&-npm)
      - [Installing Dependency packages](installing-dependency-packages)
      - [Generating Random Password](generating-random-password)
      - [Cloning awx repo and installation](cloning-awx-repo-and-installation)
      - [Access WEB UI](#access-web-ui)

### Overview

**`AWX`** is a free community edition of Ansible tower. It is an upstream project from which ansible tower is derived. It is free and doesn't require any license.

### Minimum Requirements

- Ubuntu 20.04
- At least 4GB of RAM – More is better
- 2vcpus – more if you have
- 10GB free disk storage

### Installation

#### Update the system

```
sudo apt update && sudo apt upgrade -y
```

#### Installing ansible

```
sudo apt install -y ansible
```

### Installing docker

```
sudo apt-get update

sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

#### Installing docker-compose

```

sudo apt install -y curl
curl -s https://api.github.com/repos/docker/compose/releases/latest \
  | grep browser_download_url \
  | grep docker-compose-Linux-x86_64 \
  | cut -d '"' -f 4 \
  | wget -qi -
chmod +x docker-compose-Linux-x86_64
sudo mv docker-compose-Linux-x86_64 /usr/local/bin/docker-compose
docker-compose version
```

#### Install nodejs & npm

```
sudo apt install -y nodejs npm
sudo npm install npm --global
```

#### Installing Dependency packages

```
sudo apt install -y python3-pip git pwgen vim
sudo pip3 install requests==2.14.2
sudo pip3 install docker
sudo pip3 install docker-compose
mkdir -p /var/lib/awx/projects
```

#### Generating Random Password

```
pwgen -N 1 -s 30
```

#### Cloning awx repo and installation

```
cd ~
cd awx/installer
git clone --depth 50 https://github.com/ansible/awx.git
```

open "inventory" file and update "admin_password" with randomly generated password.

```
ansible-playbook -i inventory install.yml -vv
```

#### Access WEB UI

AWX web ui can be accessed at http://\<IP-Addreess\>:80/#/login


