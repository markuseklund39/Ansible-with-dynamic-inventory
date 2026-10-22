# Ansible Dynamic Inventory

A small lab setup for Ansible dynamic inventory using the Nmap inventory plugin.

## Environment

- Hyper-V network with Ubuntu Server VMs
- Virtual switch connected to an external network
- **Ansible server:** static IPv4 address (`192.168.50.130`)
- **Two clients:** addresses assigned via DHCP

## Table of Contents

1. [Install Ansible](#1-install-ansible)
2. [Set up SSH keys for the clients](#2-set-up-ssh-keys-for-the-clients)
3. [Configure Ansible](#3-configure-ansible)
4. [Nmap inventory plugin](#4-nmap-inventory-plugin)
5. [Passwordless sudo on the clients](#5-passwordless-sudo-on-the-clients)
6. [Test the playbook](#6-test-the-playbook)

## Project structure

```text
~/ansible/
├── ansible.cfg
├── inventory/
│   └── nmap.yml
└── playbook/
    └── test.yaml
```

## 1. Install Ansible

Update the system and install the required packages:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y software-properties-common python3-pip pipx openssh-client nmap
```

Add the official Ansible PPA and install Ansible:

```bash
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
```

Verify the installation and install the required collections:

```bash
ansible --version
ansible-galaxy collection install community.general ansible.posix
```

## 2. Set up SSH keys for the clients

Generate an SSH key on the Ansible server:

```bash
ssh-keygen -t ed25519 -C "ansible"
```

Copy the public key to each client:

```bash
ssh-copy-id <user>@<vm-ip>
```

## 3. Configure Ansible

Create the Ansible configuration file at [~/ansible/ansible.cfg](https://github.com/markuseklund39/Ansible-with-dynamic-inventory/blob/main/ansible.cfg).

## 4. Nmap inventory plugin

Configure the Nmap inventory plugin in [~/ansible/inventory/nmap.yml](https://github.com/markuseklund39/Ansible-with-dynamic-inventory/blob/main/nmap.yml).

Test the inventory and connectivity:

```bash
ansible-inventory --graph
ansible all -m ping
```

## 5. Passwordless sudo on the clients

On each client, allow your user to run `sudo` without a password:

```bash
echo "<user> ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/<user>
sudo chmod 440 /etc/sudoers.d/<user>
sudo visudo -cf /etc/sudoers.d/<user>
```

> **Note:** `visudo -cf` validates the syntax of the file. Always run it to avoid locking yourself out of `sudo`.

## 6. Test the playbook

Run the test playbook located at `~/ansible/playbook/test.yaml`.
