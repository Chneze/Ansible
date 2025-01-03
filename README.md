# Ansible
This is my Ansible Repository !!

Ansible Notes:

- Install Openssh on all the machines.
- Install ansible on the control machine.
- Create username (ansible) in the remote machines, and add the user to the sudoers group.

   sudo adduser ansible  (assign it a strong password)
   sudo usermod -aG sudo ansible
   
Open sudoers configuration file:

sudo visudo


Add the following line into sudoers file:

ansible ALL=(ALL:ALL) NOPASSWD:ALL


- Create a key pair in the control machine.
ssh-keygen

- Copy the public key from the created keys to the remote hosts.

ssh-copy-id -i ~/.ssh/id_ed25519.pub ansible@192.168.115.128



- Create inventory.yml file on the control machine. { This file consists of the hosts to be managed by ansible and their credentials.}
- We can list all the servers, and then we can group the servers in to specific groups under the "children" section of the inventory file.

all:
  
  hosts:
     web_server1:
      ansible_host: 192.168.115.128
      ansible_port: 22
      ansible_ssh_user: ansible
      ansible_ssh_private_key_file: ~/.ssh/id_ed25519
     db_server1:
      ansible_host: 192.168.115.128
      ansible_port: 22
      ansible_ssh_user: ansible
      ansible_ssh_private_key_file: ~/.ssh/id_ed25519

  children:
    web_servers:
      hosts:
        web_server1:
    db_servers:
      hosts:
        db_server1:


- Create playbooks that store the required instructions.
  e.g. install_apache_playbook.yml

---
- hosts: all
  become: true

  tasks:

  - name: Latest apache version installed
    apt:
      name: apache2
      state: latest

  - name: Apache enabled and running
    service:
      name: apache2
      enabled: true
      state: started

- You can execute ansible as:
   ansible-playbook -i inventory.yml install_apache_playbook.yml



## Running adhoc commands:
ansible all -i inventory.yml -bK -m apt -a "name=ufw state=present"
(-b : become yes, -K : ask become password)
