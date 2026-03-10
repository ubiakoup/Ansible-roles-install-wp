# WordPress Deployment with Ansible-roles and Docker

## Objective of the Lab

The goal of this lab is to deploy a **containerized WordPress instance** on a client server using **Ansible** and an **external Ansible role**.

The deployment uses **Docker** and **Docker Compose** to orchestrate the containers required by WordPress.

The cluster used in this lab consists of:

* **1 Ansible control node (node1)** – the machine that runs Ansible
* **1 client node** – the target server where WordPress is deployed


# Architecture

Ansible Control Node (node1)

Ansible Playbook

Client Server

Docker Compose

├── wordpress

├── mysql

└── nginx


# Prerequisites

The following components must be available:

* Ansible installed on the control node
* SSH access to the client node
* Docker installed on the client node
* Internet access to download Docker images

Check the Ansible version:

```bash
ansible --version
```

---

# Cloning the Project

The project from **TP-7(https://github.com/ubiakoup/ansible-docker-apache-secure-deployment_ssh.git)** is retrieved from GitHub.

```bash
git clone https://github.com/ubiakoup/ansible-docker-apache-secure-deployment_ssh.git
cd ansible-docker-apache-secure-deployment_ssh
```

---

# Installing the WordPress Role

The role provided in the instructions is **not available on Ansible Galaxy**, therefore it must be installed directly from GitHub.

```bash
ansible-galaxy install git+https://github.com/diranetafen/ansible-role-containerized-wordpress.git -p roles
```

This creates the following structure:

```
roles/
└── ansible-role-containerized-wordpress
```

---

# Inventory Configuration

File: **hosts.yml**

```yaml
all:
  hosts:
    client:
      ansible_host: <enter your client IP>
      ansible_user: admin
```

Test the connection:

```bash
ansible -i hosts.yml client -m ping
```

---

# Creating the WordPress Playbook

File: **wordpress.yml**

```yaml
---
- name: Deploy WordPress Container
  hosts: prod
  become: true

  vars:
    system_user: admin
    compose_project_dir: /home/admin/compose-wordpress
    domain: foolcontrol.org
    stage: staging
    wp_version: 5.2.4
    wp_db_user: admin
    wp_db_psw: change-M3
    db_root_psw: change-M3
    wp_db_name: wordpress
    wp_db_tb_pre: wp_
    wp_db_host: mysql

  roles:
    - ansible-role-containerized-wordpress
```

---

# Issues Encountered and Solutions

## 1. Missing `www-data` user

The role expects the following user to exist:

```
www-data
```

However, on our **CentOS/RHEL environment**, this user does not exist by default.

Solution:

```bash
ansible -i hosts.yml client -b -a "useradd www-data"
```

---

## 2. Docker Compose not installed

The role tries to execute:

```
/usr/local/bin/docker-compose
```

However **docker-compose was not installed on the client machine**, which caused the deployment to fail.

### Installing docker-compose

Download docker-compose:

```bash
ansible -i hosts.yml client -b -a "curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-Linux-x86_64 -o /usr/local/bin/docker-compose"
```

Make it executable:

```bash
ansible -i hosts.yml client -b -a "chmod +x /usr/local/bin/docker-compose"
```

Verify installation:

```bash
ansible -i hosts.yml client -a "/usr/local/bin/docker-compose --version"
```

Expected result:

```
docker-compose version 1.29.2
```

---

# Deploying WordPress

Run the playbook:

```bash
ansible-playbook -i hosts.yml wordpress.yml
```

The role performs the following actions:

1. Creates the Docker Compose project directory
2. Generates the `docker-compose.yml` file
3. Deploys the WordPress configuration
4. Starts the containers using Docker Compose

---

# Verifying the Deployment

Check running containers:

```bash
ansible -i hosts.yml client -a "docker ps"
```

Example output:

```
wordpress
mysql
nginx
```

---

# Accessing WordPress

Open a web browser and navigate to:

```
http://CLIENT_IP:80
```

You should see the **WordPress installation page**.

---

# Final Project Structure

```
ansible-docker-apache-secure-deployment_ssh
│
├── ansible.cfg
├── hosts.yml
├── wordpress.yml
│
└── roles
    └── ansible-role-containerized-wordpress
```

---
