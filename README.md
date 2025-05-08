Ansible Setup Notes – From Install to First Ping

💪 Step-by-Step Configuration and Execution Summary
This guide outlines the complete process Sharif followed to install and configure Ansible, generate keys, connect to an Azure VM, and successfully execute a ping module using inventory groups. This setup uses a local Ansible directory with project-specific configuration and inventory files.

🔧 Step 1: Ansible Installation
    • Ansible was pre-installed and confirmed with:
      ansible --version
      Version used: 2.13.x with Python 3.8+

📁 Step 2: Create Working Directory
mkdir ~/ansible
cd ~/ansible

This directory contains all Ansible-related files: configuration, inventory, SSH keys, and playbooks.

🔐 Step 3: Generate SSH Key for Azure VM
ssh-keygen -t rsa
    • Saved key as: idrsa
    • This created:
        ◦ ~/ansible/idrsa (private key)
        ◦ ~/ansible/idrsa.pub (public key)

Note: Public key (idrsa.pub) was used during Azure VM creation.
🌐 Step 4: Create Azure VM (Outside of Ansible)
    • OS: Ubuntu LTS
    • Username: azureuser
    • Authentication: SSH key (public key pasted during creation)
    • IP Address: e.g., 20.84.123.169
🔄 Step 5: Verify SSH Access to Azure VM
ssh -i ~/ansible/idrsa azureuser@20.84.123.169

SSH connection was successful. No password required due to key authentication.
⚖️ Step 6: Create Local Ansible Config
nano ~/ansible/ansible.cfg

Content:
[defaults]
inventory = inventory.ini
host_key_checking = False

This tells Ansible to:
    • Use a local inventory.ini file
    • Skip host key confirmation prompts
📖 Step 7: Create Inventory File
nano ~/ansible/inventory.ini
Content:
nano ~/ansible/inventory.ini
Content:
[group1]
20.84.123.169 ansible_user=azureuser ansible_ssh_private_key_file=/home/cloud_user/ansible/idrsa

    • Group: group1
    • Host: Public IP of Azure VM
    • User: azureuser
    • SSH key: full path to private key
✅ Step 8: Run Ansible Ping Command
ansible group1 -m ping -v
Output:
20.84.123.169 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

Confirms that:
    • Ansible read local config
    • Parsed local inventory
    • Connected via SSH with correct key and user
    • Ran the ping module successfully

🔢 Final Folder Structure
~/ansible/
├── ansible.cfg
├── inventory.ini
├── idrsa
├── idrsa.pub

🤔 Key Concepts Learned
    • Avoid editing /etc/ansible/hosts by using inventory.ini
    • Use project-local ansible.cfg to override global settings
    • SSH key-based auth is required for cloud-hosted nodes (Azure)
    • [group] section headers must not have spaces (i.e., [group1] is valid, [ group1 ] is not)
    • You can use IP-only inventories but must pass -u and --private-key on every command if you do


Then for the next part ::::::::::
📘 Ansible Playbooks – Practical Automation Guide
This README documents the purpose, structure, and execution details of a beginner-friendly Ansible project for automating common server tasks. This project is built using local Ansible configuration with key-based SSH access.


✅ Project Highlights
    • SSH key authentication to remote VMs
    • Simplified inventory and centralized ansible.cfg
    • Playbooks for service management, user creation, and remote script execution
    • Clean project structure



📁 Project Structure
~/ansible/
├── ansible.cfg              # Local Ansible configuration
├── inventory.ini            # Simplified inventory with IPs only
├── idrsa / idrsa.pub        # SSH key pair for Azure VM
├── files/                   # Folder for external scripts
│   └── demo-script.sh
└── playbooks/               # Collection of playbooks
    ├── 01-ping.yml
    ├── 02-user-create.yml
    ├── 03-package-install.yml
    ├── 04-service-handler.yml
    └── 05-copy-script-execute.yml

🚀 Playbooks Overview
🔹 01-ping.yml
- name: Ping all hosts
  hosts: all
  tasks:
    - name: Ping test
      ping:

Purpose: Test SSH + Ansible connection to all listed hosts.

🔹 02-user-create.yml
Purpose: Creates a deploy user on the remote machine with a bash shell.

🔹 03-package-install.yml
Purpose: Installs nginx using the apt module on Ubuntu systems.

🔹 04-service-handler.yml
Purpose: Starts nginx and ensures it's enabled on boot.

🔹 05-service-handler.yml
Purpose: Stops nginx and ensures it's enabled on boot.

🔹 06-copy-script-execute.yml
Purpose: Copies a shell script from your local machine to the VM and executes it.

🧪 How to Run the Playbooks
From ~/ansible directory, run:
ansible-playbook playbooks/01-ping.yml
ansible-playbook playbooks/02-user-create.yml
ansible-playbook playbooks/03-package-install.yml
ansible-playbook playbooks/04-service-handler.yml
ansible-playbook playbooks/05-copy-script-execute.yml


🏁 Prerequisites
    • ansible.cfg must include:
[defaults]
inventory = inventory.ini
remote_user = azureuser
private_key_file = ./idrsa
host_key_checking = False

    • Azure VM must allow SSH (port 22) and use the idrsa.pub key



      
