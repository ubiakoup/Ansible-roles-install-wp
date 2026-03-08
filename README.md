# Ansible Secure Web Application Deployment

This project demonstrates how to automate the deployment of a web application using **Ansible**, while following **security best practices** such as **Ansible Vault for secret management** and **SSH key authentication**.

The environment consists of an **Ansible control node** and a **managed client node** where the application is deployed.

---

## 🚀 Project Objectives

This lab demonstrates how to:

- Automate application deployment using **Ansible**
- Secure sensitive information using **Ansible Vault**
- Use **SSH key-based authentication**
- Structure an Ansible project following best practices
- Deploy infrastructure in a **repeatable and automated way**

---

## 🏗 Architecture

```

Ansible Control Node
│
│  SSH (key authentication)
▼
Managed Client Node
│
▼
Docker Container running Apache Web Server

```

---

## 📁 Project Structure

```

ansible-docker-apache-webapp-deployment/
│
├── deploy.yml
├── hosts.yml
├── files/
│   └── secrets/
│       └── credentials.vault
│
├── templates/
|    └── index.html.j2
├── group_vars/
│   └── prod
└── README.md

````

---

## 🔐 Secret Management with Ansible Vault

Sensitive variables (like admin passwords) are stored inside a **vault-encrypted file**.

### Encrypt the vault file

```bash
ansible-vault encrypt files/secrets/credentials.vault
````

### Edit encrypted secrets

```bash
ansible-vault edit files/secrets/credentials.vault
```

### Load secrets in the playbook

```yaml
vars_files:
  - files/secrets/credentials.vault
```

---

## 🔑 SSH Key Authentication

Generate an SSH key pair:

```bash
ssh-keygen -t rsa
```

Copy the public key to the managed node:

```bash
cat ~/.ssh/id_rsa.pub >> /home/admin/.ssh/authorized_keys
```

Disable host key checking in the inventory:

```yaml
all:
  vars:
    ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
```

---

## ▶️ Running the Playbook

Execute the deployment:

```bash
ansible-playbook -i hosts.yml deploy.yml --ask-vault-pass
```

You will be prompted to enter:

* your **sudo password**
* your **vault password**

---

## ✅ Verification

After execution, verify:

* The playbook finished successfully
* The container is running on the client node
* The web application is accessible

Example:

```bash
docker ps
```

---

## 🛠 Technologies Used

* Ansible
* Ansible Vault
* Docker
* Apache
* Linux
* SSH

👤 Author

**Ulrich Kouatang**
Industrials Devops Engeneer|Industial IoT

